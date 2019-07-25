# HOPE 3 -- Dependency Injection

|             |                                             |
| ------------| ------------------------------------------- |
| HOPE:       | 3                                           |
| Title:      | Dependency Injection                        |
| Author(s):  | Timothy Crosley <timothy.crosley@gmail.com> |
| Status:     | Provisional                                 |
| Type:       | Standard Track                              |
| Created:    | 25-May-2019                                 |
| Updated:    | 27-May-2019                                 |

## Abstract

This HOPE proposes adding a robust and universally available dependency injection system to Hug.
This system would be built with the intention of replacing Hug's current `directive` system, one of the first Python microservice dependency injection systems, solving the shortcomings identified upon extensive usage:

- Directives aren't clearly separated from type annotations.
- It is impossible to have both a directive and type hint for a single parameter.
- It isn't easy to swap out directives for full HTTP stack testing (through direct calls make it easy to substitute out).
- Directives aren't nestable.

## Proposed Dependency Injection System

This HOPE proposes a new dependency injection system with an aim for clarity and reuse.
The API of the new system would consist of a `provide` decorator for defining available dependencies, and a `require` function for using them:

```python
@provide(name: Union[FunctionName, bool, str] = FunctionName, *, api: hug.API = None, override: bool = False)

require(name_or_function: Union[str, Callable], *args, **kwargs) -> Any
```

These two calls are meant to replace the old `Directive` system:

```python
@directive(apply_globally: bool = False, api: hug.API = None)
#def timer(...):
#   pass
```

Which was used in the following manner:

```python
@hug.get()
def my_endpoint(hug_timer):
    pass


# OR in the more modern form:


@hug.get()
def my_endpoint(timer: hug.directives.timer):
    pass
```


### Defining a dependency

Defining a dependency will happen via a new hug `provide` decorator:

```python
    @hug.provide
    def mysql_connection(host, port, ...):
        ...
```

By default, the name will be the name of the function, but the first argument sent to that directive should enable redefining it:

```python
    @hug.provide("mysql")
    def mysql_connection(host, port, ...):
        ...
```

It should also be possible to specify different dependency providers for different interfaces:

```python
    # Multiple interfaces using one provider
    @hug.provide("mysql", interfaces=['http', 'websocket'])
    def mysql_web():
        ...


    # For a provider for a single interface, this shorthand can be used
    @hug.cli.provide("mysql")
    def mysql_cli():
        ...


    # The following would also be a valid way to apply to multiple interfaces
    @hug.http.provide("mysql")
    @hug.cli.provide("mysql")
    def mysql():
        ...

    # Or the following would also be a valid way to apply to multiple interfaces
    @hug.http.provide
    @hug.cli.provide
    def mysql():
        ...
```

If you only want to enable provider usage locally, and don't want to allow it to be overridden, you can explicitly set it to stay unnamed:

```python
    @hug.provide(name=False)
    def mysql_connection(host, port, ...):
        ...
```

### Using a dependency

Dependencies will be pulled into individual endpoints via a `require` decorator, and provided to the calling function
via an eponymous argument

```python

@hug.http
@hug.require.mysql
def hello_world(mysql):
    pass
```

Arguments that are passed to the `require` function will be passed directly to the `provide` function.
This will be done in the same manner as Python's `funtools.partial` and follow the same function signature.
Any non-provided arguments will then require the dependency to pull them from the current application action,
like for normal hug calls, or via dependency injection. A key point: Any dependency can have unlimited sub-dependencies.

For unnamed dependencies, the function itself should be passed into the require function:

```python
import json


@hug.provide(scope="module"):
def shared_configuration(config_file_location="config_file.json"):
    with open(config_file_location) as config_file:
         return json.loads(config_file.read())


@hug.http
@hug.require.shared_configuration
def hello_world(shared_configuration):
    pass
```

If a named dependency is passed to require in this fashion, it will raise an `InvalidNamedDependencyUsage` exception.

### Where registered dependency providers are stored

Dependencies providers will be stored within the Hug API module level singleton, within a `dependency_providers` dictionary.
If a second `provide` function is defined by default it will raise an `ExistingDependency` exception. However, if that second definition defines `override=True`, it will simply take the place of the first - similar to defining a second function with the same name. If a dependency provider is only meant for a particular set of interfaces, it will be stored on the sub interface API object within the singleton. Hug will first check the per-interface dict of providers, and only fall through to the global dict if the interface specific one does not have the requested provider.

### Overriding a dependency

Overriding a dependency in this system is straight forward: You update the dictionary to point to your new dependency providing function. You do this by creating a new provide against the existing provide API singleton, with the `override` parameter set to `True`.
For instance, in `py.test`'s [conftest.py](https://docs.pytest.org/en/2.7.3/plugins.html?highlight=re#conftest-py-local-per-directory-plugins), you could store a series of dependency providers targeting the API you intend to test:

```python
   @hug.provide("mysql", override_api=production_api_im_testing)
   def replace_mysql_with_mock():
       return mock.MagicMock()
```

## Rejected Ideas:

### Unnamed (loose) dependencies

*The following was rejected because it over-complicated the concept, leading to more new APIs then required.*

Let's say you reuse a single set of parameters for every endpoint within an API module. Currently, in Hug, the simplest thing to do is redefine these parameters in every function.
This is inconsistent with the Don't Repeat Yourself (DRY) principle. In this HOPE we are proposing to allow automatic nesting of any hug decorated endpoint in the same manner as full dependencies,
albeit without the ability to easily swap them out for testing. Here's the proposed API for this feature:

```python
@hug.http
def my_first_endpoint(argument_1, argument2):
    ...


@hug.http
def my_second_endpoint(first_two_arguments: Dict=my_first_endpoint, argument_3):
    ....
```

This would provide a natural and straight forward way to compose together API endpoints for optimal reuse.
