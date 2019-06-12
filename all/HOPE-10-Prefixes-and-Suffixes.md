# HOPE 10 -- Prefixes and Suffixes

|             |                                             |
| ------------| ------------------------------------------- |
| HOPE:       | 10                                          |
| Title:      | Prefixes and Suffixes                       |
| Author(s):  | Timothy Crosley <timothy.crosley@gmail.com> |
| Status:     | DRAFT                                       |
| Type:       | Standards Track                             |
| Created:    | 11-June-2019                                |
| Updated:    | 11-June-2019                                |

## Abstract

This HOPE proposes new standardized behavior for Hug prefixes and suffixes.

## Problem Statement

Currently, Hug interface routers expose a `prefixes` and `suffixes` argument to allow supporting multiple extensions,
but they don't enable cases were you don't want to expose the original endpoints.
This behavior is also currently not well defined across interface types and the logic behind the behavior not well
layed out. This is well illustrated by [this hug ticket inquiring about the behavior](https://github.com/hugapi/hug/issues/451).

## Proposed Solution

All interface routers that expose the concept of `prefixes` and `suffixes` should accept the arguments against the following signature:

```python
@interface_router(prefixes: Union[List, str, None], suffixes: Union[List, str, None])
```

If a string is passed in, it should be handled identically to that same string being passed in as the only element in a list: `[str]`.
If `suffixes` or `prefixes` are set, the router should replace the current endpoint list with one that is expanded to include each of the provided
`suffixes` and `prefixes`. This would mean by default, the non-suffixed  or prefixed endpoints would no longer be accessible.

The old behavior would still be achievable simply by explicitly adding empty items to the list.

### Old:

```
@hug.get(suffixes=["json", "html"])
def my_endpoint():
    ...
```

### New:

```
@hug.get(suffixes=["json", "html", ""])
def my_endpoint():
    ...
```
