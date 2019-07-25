# HOPE 6 -- Per Interface Defaults

|             |                                             |
| ------------| ------------------------------------------- |
| HOPE:       | 6                                           |
| Title:      | Per Interface Defaults                      |
| Author(s):  | Timothy Crosley <timothy.crosley@gmail.com> |
| Status:     | Provisional                                 |
| Type:       | Standards Track                             |
| Created:    | 1-June-2019                                 |
| Updated:    | 1-June-2019                                 |

## Introduction

Currently, there is no way in Hug to change defaults quickly per an interface an endpoint is exposed on.
Doing so requires either middleware usage or multiple endpoints, needlessly complicating otherwise simple APIs.
This HOPE details the addition of per-interface default parameter support to Hug.

## Implementation

Each interface decorator would allow addition of arbitrary kwargs to pass as defaults to the api function, should
the api function return None.

JMT TODO: Is the description above accurate? Or maybe None is allowed, but interface defaults always take precedence over functional defaults.

Looking somewhat like this:


```python
@hug.http(user="system")
@hug.cli()
def log_message(message, user=getpass.getuser()):
    ...
```

The defaults would also be able to be applied to parameters whose endpoints have None defined:


```python
@hug.http()
@hug.cli(user=getpass.getuser())
def log_message(message, user):
    ...
```

In the above example, this would mean that when calling from the CLI, no user would be required, but it would be necessary when making an HTTP API call.

## Rejected Proposals

### Using *kargs and **kwargs for defaults

*This section of the HOPE was rejected as it was determined that this could lead to naming conflicts as more parameters are added to the routers. This is a large cost for the minimal benefit of not needing to explicitly specify defaults.*

This HOPE proposes utilizing unused **kwargs in the interface decorators as automatic defaults.
This would simplify the above to:

```python
@hug.http()
@hug.cli(user=getpass.getuser())
def log_message(message, user):
    ...
```
