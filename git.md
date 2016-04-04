Git
===========================================
Every project at the Nerdery MUST be tracked by a Version Control System (VCS). The RECOMMENDED VCS for the Nerdery is Git. VCS Systems MUST be listed under the Client page on Mainframe under the Systems tab, and SHOULD be connected to a specific project.

Ignoring Files
-------------------------------------------
RECOMMENDED .gitignore:
https://github.com/github/gitignore/blob/master/VisualStudio.gitignore

RECOMMENDED .gitattributes:
https://github.com/Danimoth/gitattributes/blob/master/VisualStudio.gitattributes

The desired result is to keep build artifacts and developer-local settings from being kept under source control. This alleviates merge conflicts when pulling compiled code down, and keeps one developer’s personal preferences for settings. 

In the general case, the nuget packages folder should not be kept in source control. However, in some unique deployment scenarios or due to client infrastructure requirements, this recommendation can be ignored on a project by project basis.
Secrets (passwords, API tokens, etc)
See the task force documentation on Configuration

Branching Strategy
-------------------------------------------
For vanilla git repositories (not utilizing Bitbucket, Github or Gitlab), the git flow strategy MUST be used. (see http://nvie.com/posts/a-successful-git-branching-model/). Tools such as SourceTree and git flow command line are RECOMMENDED for ease of use. 

Git based repositories that support a pull request model (eg. Bitbucket, Github) MAY opt to not use the git flow model, and may instead use the Github flow model (see https://guides.github.com/introduction/flow/).
Releases
All releases SHOULD be tagged in git with an appropriate release/{version} tag. An exception MAY be made If releases are so frequent this would lead to tag overload, such as when continuous delivery strategies are being implemented.
