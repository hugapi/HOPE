# HOPE 7 -- Automatic Testing

|             |                                             |
| ------------| ------------------------------------------- |
| HOPE:       | 7                                           |
| Title:      | Automatic Testing                           |
| Author(s):  | Timothy Crosley <timothy.crosley@gmail.com> |
| Status:     | Proposed                                    |
| Type:       | Standards Track                             |
| Created:    | 3-June-2019                                 |
| Updated:    | 4-June-2019                                 |

## Abstract

This HOPE proposes adding automatic endpoint testing to Hug.
Automatic endpoint testing would be powered by the properties and examples specified on an endpoint.

## Problem Statement

Currently, Hug utilizes Type annotations to automatically validate and document APIs.
This documentation even extends to providing optional examples for individual API endpoints.
However, currently Hug doesn't provide tests to ensure these examples, or the API overall is operational as intended.
This represents a missing opportunity to simplify a currently complex and common API development task:

From the [Zen of Hug](https://github.com/hugapi/HOPE/blob/master/all/HOPE-20--The-Zen-of-Hug.md):

```
Simple Things should be easy, complex things should be possible.
Complex things done often should be made simple.

```

## Expanding Example Support

This first step to enable built-in automatic testing support is to expand the set of examples available.
Currently examples take the form of either a single URL or a list of URLS:

```python
    # one URL
    @hug.get(examples="name=HUG&age=1")

    # two URLs - also valid
    @hug.get(examples=["name=HUG&age=1", "name=Timothy&age=29"])
```

These examples are limited in their expressiveness and are only implemented in a single interface (HTTP).

