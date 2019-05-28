# HOPE 3 -- Dependency Injection

|             |                                             |
| ------------| ------------------------------------------- |
| HOPE:       | 3                                           |
| Title:      | Dependency Injection                        |
| Author(s):  | Timothy Crosley <timothy.crosley@gmail.com> |
| Status:     | Proposed                                    |
| Type:       | Standard Track                              |
| Created:    | 25-May-2019                                 |
| Updated:    | 25-May-2019                                 |

## Abstract

This HOPE proposes adding a robust and universally available dependency injection system to Hug.
This system would be built with the intention of replacing Hug's current `directive` system, one of the first Python microservice dependency injection systems, solving the short comings identified upon extensive usage:

    - Directives aren't clearly separated from type annotations.
    - It is impossible to have both a directives and a type requirement for a single parameter.
    - It isn't easy to swap out directives for full HTTP stack testing (though direct calls make it easy to substitute out).
    - Directives aren't nestable.

## Proposed Depency Injection System

This HOPE proposes a new dependency injection systems that aims for clarity and reuse.

### Defining a depency

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

### Using a dependency

Dependencies will be added via a `depends_on(` function, that will be called within function/method defaults:

```
from hug import depends_on

@hug.http()
def hello_world(mysql=depends_on('mysql')):
    pass
```

Arguments can be passed to the `depends_on` function, these will be passed directly to the `provides` function. Any non-provided arguments, will then require the dependency to pull them from the current application action, like for normal hug calls, or via dependency injection. A key point: Any dependency can have unlimited sub dependencies.

### Where dependencies are stored

Dependencies will be stored withing the Hug API module level singleton, within a `dependency_providers` dictionary. If a second `provides` function is defined it will simply take the place of the first - simular to defining a second function with the same name.

### Overriding a dependency

Overriding a dependency in this system is straight forward: You update the dictionary to point to your new dependency providing function. For instance, in `py.test`'s conftest.py, you could store a series of dependency providers targeting the API you intend to test:

```
   @hug.provides("mysql", api=production_api_im_testing)
   def replace_mysql_with_mock():
       return mock.MagicMock()
```

### Unamed (loose) dependencies

Let's say you reuse a single set of parameters for every endpoint within an API module. Currently, in Hug, the simplist thing to do is redefine these parameters in every function. This is inconsistent with the Do It Right ONCE (DRY) principle. In this HOPE we are proposing to allow automatic nesting of any hug decorated endpoint in the same mannor as full dependencies, albeit without the ability to easily swap them out for testing. Here's the propsed API for this feature:

```
@hug.http()
def my_first_endpoint(argument_1, argument2):
    ...


@hug.http()
def my_second_endpoint(first_two_arguments: Dict=my_first_endpoint, arguement_3):
    ....
```

This would provide a natural and straight forward way to compose together API endpoints for optimal reuse.
