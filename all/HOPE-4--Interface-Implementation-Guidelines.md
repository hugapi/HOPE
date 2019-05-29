# HOPE 4 -- Interface Implementation Guidelines

|             |                                             |
| ------------| ------------------------------------------- |
| HOPE:       | 4                                           |
| Title:      | Interface Implementation Guidelines         |
| Author(s):  | Timothy Crosley <timothy.crosley@gmail.com> |
| Status:     | Proposed                                    |
| Type:       | Informational                               |
| Created:    | 28-May-2019                                 |
| Updated:    | 29-May-2019                                 |

## Introduction

This HOPE outlines best practices for defining new Hug interfaces, and how Python functionality should be mapped to individual interfaces across Hug. It does so at a high-level, with specific code implementations intended for future Standards Track HOPEs.

## What are Hug Interfaces?

In Hug, Interfaces represent ways that you can expose your existing code for external use. This external use could be from within other internal or external projects over native Python calls, over HTTP using REST APIs, via the command line, or any other way that your code and business logic can be mapped for external use. It is the glue between your code and your consumers. Interfaces are Hug's primary abstraction: to empower users to keep reuse implementations and business logic while encouraging interface details to never spill into them. In a way, you can think of Hug as utilizing interfaces to map to the same ideals as [sans I/O](https://sans-io.readthedocs.io/) at the API level.

```
    API <- Interface(s) <- Consumers
```

## Interface Implementations -> Interface Category Defaults

Individual interfaces should target frameworks or specific implementations and not the overarching type of interface.

For example:

There could be an interface implementation created for `ArgParse` or `click`. Both of these represent interface implementations within the wider Command Line interface type. For Hug's purposes, we would first create an `ArgParse` or `click` interface implementation, and then alias that implementation to be the default for the `cli` interface category. This is intended to allow interface implementations to change over time, with the most promising current implementation used.

### What are the basic requirements for a Hug Interface?

Hug interfaces need at a minimum to be able to map to Python function definitions.
This means they need to allow:

- Parameterized and validated input/output. Or a standard way to simulate the same.
- The ability to run arbitrary Python code to process input and produce output.

Ideally, any interface should allow cleanly mapping to Python concepts without being forced.

### Tight Mapping Between Python Behaviour and Interfaces

Hug's value comes from mapping Python's expressive syntax for defining interfaces using modules, functions, classes, and methods to idiomatic implementations of external interfaces.
To best achieve this goal, interfaces implementations should conform to Python behavior as closely as possible.
Whenever there is a choice between implementing custom behavior for an interface and implementing behavior that is consistent with that seen in native Python usage, the native Python usage should be applied.

#### Illustrative Examples:
*No extra arguments*:

Python's behavior for not allowing extra arguments by default should, if possible, be followed by interfaces.

For Example:

```python

        @hug.http()
        def my_endpoint():
            pass
```

Should fail if any arguments are passed into the endpoint.

```python

        @hug.http()
        def my_endpoint(**kwargs):
            pass
```

Can be used to allow extra arguments to be passed in.

*No extra arguments by default*:

Python's behavior for not allowing extra arguments by default should, if possible, be followed by interfaces.

For Example:

```python

        @hug.http()
        def my_endpoint():
            pass
```

Should fail if any arguments are passed into the endpoint.

```python

        @hug.http()
        def my_endpoint(**kwargs):
            pass
```

Can be used to allow extra arguments to be passed in.

*Arguments Passed in by Order OR Name*:


```python

        @hug.cli()
        def add(number_1: int=1, number_2: int=1) -> int:
            return number_1 + number_2
```

Should have the following behaviour:

| Execution           | Output                                      |
| --------------------| ------------------------------------------- |
| `add 1 2`           | `3`                                         |
| `add 4`             | `5`                                         |
| `add`               | `2`                                         |
| `add --number_2 3`  | `4`                                         |

### Full Power of the Interface Implementation Available if Needed

From the [Zen of Hug](https://github.com/hugapi/HOPE/blob/master/all/HOPE-20--The-Zen-of-Hug.md):

```
    Simple Things should be easy, complex things should be possible.
    Complex things done often should be made simple.
```

While Hug interfaces aim to simplify initial API exposure to the bare minimum, it should always enable using the full power available for any individual interface implementation.

This means, at a minimum:

- Any frameworks, libraries, and data structures that power an interface should be modifiable completely on API startup, before being exposed.
- Specific endpoints should be able to entirely modify interaction with interface implementations outside of the reusable Python based endpoint definition.
- Interfaces should provide parameters for custom modifications within their routers.
