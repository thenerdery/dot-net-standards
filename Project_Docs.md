# Project Documentation

## README.md

Each project MUST have a README markdown file explaining the purpose of the
project and a quick overview of the architecture or notable patterns used.  The
readme file SHOULD also include relevant external links and references to other
documentation internal to the project.

Common sections of a README file are.

#### Project Description

Explanation of what the project accomplishes and how it fits into the business.

This section MUST exist.

#### Environment Setup

Steps a new developer will need to take to get the project running locally for
development.

This section MUST exist if the project needs any more setup than opening the
solution and pressing `F5`.

#### Deployment

Instructions on how to deploy the application.

This section MUST exist if we are responsible for deployments. If the client
does deployment themselves, this section can be omitted or replaced with a note
indicating that fact.

#### External Systems

Any systems that the project requires externally (eg. SQL databases, API
credentials).

Passwords or authorization keys for external services MUST NOT be in the README.
They MUST be included in the Systems tab in Mainframe.

This section MAY exist.

#### Project-specific code standards

Many projects, especially those touched by more than one developer team, may
have different code styles in use.  To ensure the Consistency Principle is
enforced, the README.md SHOULD clarify what standards, patterns, and techniques
are to be propagated with new code if they deviate from these standards.

### Branching Out

A large project may outgrow a single readme file.  In this case the readme
SHOULD point to a docs folder in the project or other appropriate location
(e.g., github wiki repo or Confluence) containing additional markdown files.  A
RECOMMENDED tool to assist in the display and navigation of large amounts of
markdown files is mdwiki.

For example:

```
Project
        docs/
                Architecture.md
                Conventions.md
                Deployment.md
        README.md
        CHANGELOG.md
```

## Confluence

We are currently running a beta with Confluence at
https://knowledge.nerdery.com/.

Larger or longer running projects may find it useful to keep supporting
documentation in Confluence. If you are using confluence, you SHOULD include a
link to the Space in the README. You MUST still maintain the README document
within the project structure, but it can focus more on the code and steps
developers need to follow to get it running.

## CHANGELOG.md

Each project MAY have a markdown file noting the new features, bug fixes,
breaking changes, and enhancements for a release version and/or date.  The
purpose of this log is for future developers to gain quick understanding of
major changes to the system, possibly to aid in troubleshooting - more recent
changes are more likely to contain bugs than older more mature parts.
Reference: keep a changelog.

A changelog is RECOMMENDED for projects which have long-lasting status at the
Nerdery.

* It’s made for humans, not machines, so legibility is crucial.
* Easy to link to any section (hence Markdown over plain text).
* One sub-section per version.
* List releases in reverse-chronological order (newest on top).
* Write all dates in `YYYY-MM-DD` format. (Example: "2012-06-02" for "June 2nd,
  2012".) It’s international, sensible, and language-independent.
* Explicitly mention whether the project follows Semantic Versioning.
    * List its release date in the above format.
    * Group changes to describe their impact on the project, as follows:
    * Added for new features.
    * Changed for changes in existing functionality.
    * Deprecated for once-stable features removed in upcoming releases.
    * Removed for deprecated features removed in this release.
    * Fixed for any bug fixes.
    * Security to invite users to upgrade in case of vulnerabilities.

Example:

```
# Change Log
All notable changes to this project will be documented in this file.
This project adheres to [Semantic Versioning](http://semver.org/).

## [3.0.0] 2015-03-14
### Breaking Changes
- Gizmo API no longer accepts XML format
### Added
- Gizmo creator animations
- Gizmo schema validator
### Fixed
- (JIRA-1234) Fixes an issue where the widgets escape the boundaries
```