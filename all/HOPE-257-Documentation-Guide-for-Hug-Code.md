# HOPE 257 -- Documentation Guide for Hug Code

|             |                                             |
| ------------| ------------------------------------------- |
| HOPE:       | 257                                         |
| Title:      | Style Guide for Hug Code                    |
| Author(s):  | Timothy Crosley <timothy.crosley@gmail.com> |
| Status:     | Proposed                                    |
| Type:       | Process                                     |
| Created:    | 23-May-2019                                 |
| Updated:    | 23-May-2019                                 |

## Introduction

This document gives documentation conventions for the Hug code comprising the Hug core as well as all official interfaces, extensions, and plugins for the framework.
Optionally, projects that use Hug are encouraged to follow this HOPE and link to it as a reference.

From the [Zen of Hug](https://github.com/hugapi/HOPE/blob/master/all/HOPE-20--The-Zen-of-Hug.md):

```
    Wrong documentation is worse than no documentation.
    Everything should be documented.
```

To this aim Hug projects should contain extensive automated low-level documentation as well as maintained high level documentation.

## PEP 257 Doc String Format

All doc string based should follow Python's [PEP 257](https://www.python.org/dev/peps/pep-0257/) doc string conventions.

## Minimal Documentation

Any code written that is intended for external use, should be documented with a doc string, at every level:
    - Module
    - Class
    - Function
    - Method
    - Etc...

Type annotations should be used heavily where able to define acceptable parameters.
These annotations should be enforced using `MyPy`.
Any documentation that can be made via Type Annotations should be made there and not repeated within doc strings.
Where possible, high level documentation should be added to module doc strings and where applicable via HOPE documents themeselves.
Any module that is a direct implementation of a HOPE should link back to the HOPE for reference.





