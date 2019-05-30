# HOPE 5 -- Type Annotation

|             |                                             |
| ------------| ------------------------------------------- |
| HOPE:       | 5                                           |
| Title:      | Type Annotation                             |
| Author(s):  | Timothy Crosley <timothy.crosley@gmail.com> |
| Status:     | Proposed                                    |
| Type:       | Standards Track                             |
| Created:    | 29-May-2019                                 |
| Updated:    | 29-May-2019                                 |

## Introduction

This HOPE outlines Hug's intended type annotation behavior.

## Strict Python Compatibility

Hug type annotations are built on top of Python 3's Type annotation system and ensure full compatibility with [PEP 484](https://www.python.org/dev/peps/pep-0484/).
Additionally, endpoints decorated with annotations should be done in a way that is fully compatible with common static analysis tools and type checkers in particular MyPy.

## Annotations Used for Validation

Annotations defined in Hug endpoints will automatically be applied to validate API calls accross interfaces.
Additionally, when possible, these types will also be used to automatically coerce parameters into the correct type.
If an error occurs at either of these steps, Human readable errors should be presented to consumers by default.

## Annotations Used for Documentation

All annotations will be used to create automatic documentation across all interfaces.
For standard Python types, a mapping will be in place to provide human-readable documentation.
For types and validators outside of the mapping, documentation will be provided by the validators docstring.

