# HOPE 7 -- Automatic Testing

|             |                                             |
| ------------| ------------------------------------------- |
| HOPE:       | 7                                           |
| Title:      | Automatic Testing                           |
| Author(s):  | Timothy Crosley <timothy.crosley@gmail.com> |
| Status:     | Proposed                                    |
| Type:       | Standards Track                             |
| Created:    | 3-June-2019                                 |
| Updated:    | 5-June-2019                                 |

## Abstract

This HOPE proposes adding automatic endpoint testing to Hug.
Automatic endpoint testing would be powered by the properties and examples specified on an endpoint.

## Problem Statement

Currently, Hug utilizes type annotations to validate and document APIs automatically.
This documentation even extends to providing optional examples for individual API endpoints.
However, currently, Hug doesn't provide tests to ensure these examples, or the API overall is operating as intended.
This represents a missing opportunity to simplify a currently complex and common API development task:

From the [Zen of Hug](https://github.com/hugapi/HOPE/blob/master/all/HOPE-20--The-Zen-of-Hug.md):

```
Simple Things should be easy, complex things should be possible.
Complex things done often should be made simple.

```

## Expanding Example Support

This first step to enable built-in automatic testing support is to expand the set of examples available.
Currently, examples take the form of either a single URL or a list of URLs:

```python
    # one URL
    @hug.get(examples="name=HUG&age=1")

    # two URLs - also valid
    @hug.get(examples=["name=HUG&age=1", "name=Timothy&age=29"])
```

These examples are limited in their expressiveness and are only implemented in a single interface (HTTP).
Additionally, example's don't provide a mechanism for specifying what the result of the example is expected to be.

As part of this HOPE, I am proposing to expand Hug's example support, to fully express all endpoint arguments.
To enable this an `Example` object would be introduced that takes args and kwargs:

```
    @hug.get(examples=[Example("argument1", "argument2", argument_1=argument_1, ..)]
```

Finally, the expected output can be defined by specifying what the example's output should be:

```
    @hug.cli(examples=[Example(1, 1) == 2, Example(2, 2) == 4)
    def add(number_1: int, number_2: int) -> int:
        return number_1 + number_2
```

## Automatic Endpoint Test Behaviour

Automatic tests can be generated for any endpoint that meets ANY of the following criteria:

    - Has at least one example
    - Contains no required arguments
    - Has type annotation for all required parameters AND hypothesis is installed.

To test any endpoint that fits this criteria, an auto function will be added to Hug's test module.

```hug.test.auto(api.add)```

The tests will return an assertion error if the output doesn't match the expected output, throw any exceptions thrown by running the test, or return True if successful.
[hypothesis](https://hypothesis.readthedocs.io/en/latest/) will be used to automatically generate test values in addition to any examples that are present if it is installed.
To enable fully deterministic test cases that only use provided examples, a second test function will be added:

```hug.test.examples(api.add)```

This endpoint will only work on endpoints that have examples defined and will otherwise raise an `InvalidTestCase` exception.

## Automatic Tests over Entire APIs

As part of this HOPE, we are intentionally leaving out support for automatic testing over all endpoints within an API.
Instead, Hug will include documentation on how to test multiple endpoints quickly using `py.test` and parameterize.
