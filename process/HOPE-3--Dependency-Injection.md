# HOPE 3 -- Dependency Injection

|             |                                             |
| ------------| ------------------------------------------- |
| HOPE:       | 3                                           |
| Title:      | Dependency Injection                        |
| Author(s):  | Timothy Crosley <timothy.crosley@gmail.com> |
| Status:     | Proposed                                    |
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
The API of the new system would consist of a `provides` decorator for defining available dependencies, and a `requires` function for using them:

```python
@provides(name: str = None, *, api: hug.API = None, override: bool = False)

requires(name_or_function: Union[str, Callable], *args, **kwargs) -> Any
```

These two calls are meant to replace the old `Directive` system:

```python
@directive(apply_globally: bool = False, api: hug.API = None)
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

Defining a dependency will happen via a new hug `provides` decorator:

```python
    @hug.provides()
    def mysql_connection(request, config):
        ...
```

By default, the name will be the name of the function, but the first argument sent to that directive should enable redefining it:

```python
    @hug.provides("mysql")
    def mysql_connection(request, config):
        ...
```

It should also be possible to specify different dependency providers for different interfaces:

```python
    # Multiple interfaces using one provider
    @hug.provides("mysql", interfaces=['http', 'websocket'])
    def mysql_web():
        ...


    # For a provider for a single interface, this shorthand can be used
    @hug.cli.provides("mysql")
    def mysql_cli():
        ...


    # The following would also be a valid way to apply to multiple interfaces
    @hug.http.provides("mysql")
    @hug.cli.provides("mysql")
    def mysql():
        ...
```

### Using a dependency

Dependencies will be pulled into individual endpoints via a `requires(` function, that will be called within function/method defaults:

```python
from hug import requires

@hug.http()
def hello_world(mysql=requires('mysql')):
    pass
```

Arguments can be passed to the `requires` function, these will be passed directly to the `provides` function.
This will be done in the same manner as Python's `funtools.partial` and follow the same function signature.
Any non-provided arguments will then require the dependency to pull them from the current application action,
like for normal hug calls, or via dependency injection. A key point: Any dependency can have unlimited sub-dependencies.

Optionaly, you can also directly require any other normal Python callable:

```python
import json

from hug import requires


def shared_configuration(config_file_location="config_file.json"):
    with open(config_file_location) as config_file:
         return json.loads(config_file.read())


@hug.http()
def hello_world(config=requires(shared_configuration)):
    pass
```

### Where registered dependency providers are stored

Dependencies providers will be stored within the Hug API module level singleton, within a `dependency_providers` dictionary.
If a second `provides` function is defined by default it will raise an `ExistingDependency` exception. However, if that second definition defines `override=True`, it will simply take the place of the first - similar to defining a second function with the same name. If a dependency provider is only meant for a particular set of interfaces, it will be stored on the sub interface API object within the singleton. Hug will first check the per-interface dict of providers, and only fall through to the global dict if the interface specific one does not have the requested provider.

### Overriding a dependency

Overriding a dependency in this system is straight forward: You update the dictionary to point to your new dependency providing function. You do this by creating a new provides against the existing provides API singleton, with the `override` parameter set to `True`.
For instance, in `py.test`'s [conftest.py](https://docs.pytest.org/en/2.7.3/plugins.html?highlight=re#conftest-py-local-per-directory-plugins), you could store a series of dependency providers targeting the API you intend to test:

```python
   @hug.provides("mysql", api=production_api_im_testing, override=True)
   def replace_mysql_with_mock():
       return mock.MagicMock()
```

## Rejected Ideas:

### Unamed (loose) dependencies

*The following was rejected because it over-complicated the concept, leading to more new APIs then required.*

Let's say you reuse a single set of parameters for every endpoint within an API module. Currently, in Hug, the simplest thing to do is redefine these parameters in every function.
This is inconsistent with the Do It Right ONCE (DRY) principle. In this HOPE we are proposing to allow automatic nesting of any hug decorated endpoint in the same manner as full dependencies,
albeit without the ability to easily swap them out for testing. Here's the proposed API for this feature:

```python
@hug.http()
def my_first_endpoint(argument_1, argument2):
    ...


@hug.http()
def my_second_endpoint(first_two_arguments: Dict=my_first_endpoint, arguement_3):
    ....
```

This would provide a natural and straight forward way to compose together API endpoints for optimal reuse.
