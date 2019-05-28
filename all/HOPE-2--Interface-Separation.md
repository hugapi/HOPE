# HOPE 2 -- Interface Separation

|             |                                             |
| ------------| ------------------------------------------- |
| HOPE:       | 2                                           |
| Title:      | Interface Separation                        |
| Author(s):  | Timothy Crosley <timothy.crosley@gmail.com> |
| Status:     | Proposed                                    |
| Type:       | Standards Track                             |
| Created:    | 22-May-2019                                 |
| Updated:    | 22-May-2019                                 |

## Abstract

This HOPE proposes separating Hug into multiple Repositories and PyPI packages to support a more modular design.

## Problem Statement

Hug's intention has always been to allow users to write code in the most idiomatic Python way without tying their code to any particular interface.
This led to the inclusion of CLI support and plans to expand to support other additional interfaces.
Including multiple interface support in one package makes this ability clear, and encourages users to experiment and use the available interfaces.
However, this approach also means each new interface adds weight and potential attack surface to the project.

## Proposed Solution

This HOPE proposes separating Hug's core functionality from individual interfaces, while still providing a single package that includes all primary
interfaces as a jumping off point.

## Structure

```
                        hug_core
                         /   \
             hug_arg_parse  hug_falcon  ... other interfaces
                    |            |
                 hug_cli       hug_http  ... other interface defaults
                        \     /
                         __ __
                           |
                          hug

```

In the proposed structure: Hug's core functionality would be moved into a stand-alone `hug_core` package.
Individual concrete interfaces would be implemented to support specific frameworks or libraries for exposing an interface:
    `hug_gtk`, `hug_falcon`, `hug_qt`, etc...

Particular concrete implementations would be selected as defaults for interface types, such as `cli` and `http`.
The `hug` package would then include these packages, exposing `hug.cli`, `hug.http`, and future interface types, maintaining overall backward compatibility,
while enabling users to include only the code and dependencies, they need for their projects.

## Transition Plan

`hug_core` and individual interface repositories will be created and released as a support base for hug 3.0.0.
As individual projects stabilize the central `hug` repository will be updated to depend on them, until all functionality
has been successfully split out.
