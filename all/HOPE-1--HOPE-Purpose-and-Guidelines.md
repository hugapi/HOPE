# HOPE 1 -- HOPE Purpose and Guidelines

|             |                                             |
| ------------| ------------------------------------------- |
| HOPE:       | 1                                           |
| Title:      | HOPE Purpose and Guidelines                 |
| Author(s):  | Timothy Crosley <timothy.crosley@gmail.com> |
| Status:     | Active                                      |
| Type:       | Process                                     |
| Created:    | 13-May-2019                                 |
| Updated:    | 13-May-2019                                 |

## What is a HOPE?

HOPE stands for Hug Official Proposal for Enhancement. A HOPE is a design document providing information to the Hug community, or describing a new feature for Hug or its processes or environment. The HOPE should provide a concise technical specification of the feature and a rationale for the feature.

We intend HOPEs to be the primary mechanisms for proposing major new features, for collecting community input on an issue, and for documenting the design decisions that have gone into Hug. The HOPE author is responsible for building consensus within the community and documenting dissenting opinions.

Because HOPEs are maintained as text files in a versioned repository, their revision history is the historical record of the feature proposal.

HOPEs are modeled directly after [Python PEPs](https://www.python.org/dev/peps/), the language that powers Hug.


## HOPE Audience

The typical primary audience for HOPEs are the core developers of the Hug framework and related plugins and libraries, as well as dedicated Hug users.

However, the process is public and review and feedback is welcome from any interested parties across the Hug and Python communities.


## HOPE Types

HOPE Types

There are three kinds of HOPEs:

- A **Standards Track HOPE** describes a new feature or implementation for Hug.
- An **Informational HOPE** describes a Hug design issue, or provides general guidelines or information to the Hug community, but does not propose a new feature. Informational HOPEs do not necessarily represent a Hug community consensus or recommendation, so users and implementers are free to ignore Informational HOPEs or follow their advice.
- A **Process HOPE** describes a process surrounding Hug, or proposes a change to (or an event in) a process. Process HOPEs are like Standards Track HOPEs but apply to areas other than the Hug framework itself. They may propose an implementation, but not to Hug's codebase; they often require community consensus; unlike Informational HOPEs, they are more than recommendations, and users are typically not free to ignore them. Examples include procedures, guidelines, changes to the decision-making process, and changes to the tools or environment used in Hug development. Any meta-HOPE is also considered a Process HOPE.

## HOPE Workflow

### Hug's Core Developers
A Hug core developer is an active contributor who has been vetted and added to the [core-devs group](https://github.com/orgs/hugapi/teams/core-devs). All Hug core developers have voting ability in the HOPE process and have the ability to suggest a new developer be added to the core-dev group.

### Start with an idea for Hug

Start with an idea for Hug

The HOPE process begins with a new idea for the Hug framework. It is highly recommended that a single HOPE contain a single key proposal or new idea. Small enhancements or patches often don't need a HOPE and can be injected into the Hug development workflow with a pull-request to one of Hug's git repositories. The more focused the HOPE, the more successful it is likely to be. If in doubt, split your HOPE into several well-focused ones.

Each HOPE must have a champion -- someone who writes the HOPE using the style and format described below, shepherds the discussions in the appropriate forums, and attempts to build community consensus around the idea. The HOPE champion (a.k.a. Author) should first attempt to ascertain whether the idea is HOPE-able. Posting to [Hug's gitter room](https://gitter.im/timothycrosley/hug) or adding an issue to the [HOPE proposal repository](https://github.com/hugapi/HOPE) is the best way to go about this.

Vetting an idea publicly before going as far as writing a HOPE is meant to save the potential author time.  Asking the Hug community first if an idea is original helps prevent too much time being spent on something that is guaranteed to be rejected based on prior discussions (searching the internet does not always do the trick). It also helps to make sure the idea is applicable to the entire community and not just the author. Just because an idea sounds good to the author does not mean it will work for most people in most areas where Hug is used.

Once the champion has asked the Hug community as to whether an idea has any chance of acceptance, a draft HOPE should be presented to this repository as a pull request with a [DRAFT] tag. This gives the author a chance to flesh out the draft HOPE to make properly formatted, of high quality, and to address initial concerns about the proposal.

## Submitting a HOPE

Following a community discussion, the standard HOPE workflow is:

- You, the HOPE author, fork the HOPE repository and create a file named HOPE-9999.md that contains your new HOPE. Use "9999" as your draft HOPE number.
- In the "Type:" header field, enter "Standards Track", "Informational", or "Process" as appropriate, and for the "Status:" field enter "Draft".
- Push this to your GitHub fork and submit a pull request.

- A core-dev will act as an editor and review your PR for structure, formatting, and other errors.
    - Approval criteria are:
        - It is sound and complete. The ideas must make technical sense.
        - The title accurately describes the content.
        - The HOPE's language (spelling, grammar, sentence structure, etc.) and code style should be correct and conformant.
        - First-pass reviewers are generally quite lenient about this initial review, expecting that problems will be corrected by the reviewing process. Note: Approval of the HOPE is no guarantee that there are no embarrassing mistakes! Correctness is the responsibility of the authors and reviewers.

        If the HOPE isn't ready for approval, an editor will send it back to the author for revision, with specific instructions.
        Once approved, your HOPE will be assigned a unique number, added to the index, and given an appropriate status, generally opening it up for further review.

## HOPE review process
- Once the initial editing review process is complete, and a core-dev approves it for review (note that this is not the same as accepting your HOPE!), they will remove the [DRAFT] tag and open the HOPE up for broader review.
- As updates are necessary, the HOPE author can check in new versions to their branch
- HOPEs will be left as open Pull Requests until either 2 core-devs have approved the HOPE, without any no votes, or a majority of core-devs have approved it.
- If a HOPE is accepted it will be merged in and placed in the `accepted` directory, otherwise, it will be merged in and placed in the rejected directory.

## Proposing changes to a HOPE
HOPEs are living documents and can be updated. To propose a change to an existing HOPE a pull request should be made with the changes in place. These changes follow the same approval guidelines as the creation of new HOPEs. In general, changes should only be made for minor differences, grammar or code example fixes, or other changes that align to the original intention of the HOPE. Anything beyond these types of changes should receive its own new HOPE.

**IMPORTANT NOTE:** Until sufficient structure is in place, I Timothy Crosley, as the Benevolent Dictator For Now (BDFN), can accept and put in place any HOPEs unanimously. I am doing this only to bootstrap the process.

## HOPE format
- All HOPEs should be UTF-8 encoded markdown files. These files should be titled HOPE-{hope_number}--{hope_title}.md, with 9999 acting as a placeholder number for new HOPEs.
