# HOPE 6 -- Per Interface Defaults

|             |                                             |
| ------------| ------------------------------------------- |
| HOPE:       | 6                                           |
| Title:      | Per Interface Defaults                      |
| Author(s):  | Timothy Crosley <timothy.crosley@gmail.com> |
| Status:     | Proposed                                    |
| Type:       | Standards Track                             |
| Created:    | 1-June-2019                                 |
| Updated:    | 1-June-2019                                 |

## Introduction

Currently, there is no way in Hug to change defaults quickly per an interface an endpoint is exposed on.
Doing so requires either middleware usage or multiple endpoints, needlessly complicating otherwise simple APIs.
This HOPE details the addition of per-interface default parameter support to Hug.

## Implementation

Each interface decorator would add a new, optional, defaults argument. This argument would take a dictionary of
parameter names and their intended defaults per the interface.

Looking somewhat like this:


```python
@hug.http(defaults={'user': 'system'})
@hug.cli()
def log_message(message, user=getpass.getuser()):
    ...
```

The defaults would also be able to be applied to parameters whose endpoints have None defined:


```python
@hug.http()
@hug.cli(defaults={'user': getpass.getuser()})
def log_message(message, user):
    ...
```

In the above example, this would mean that when calling from the CLI, no user would be required, but it would be necessary when making an HTTP API call.
