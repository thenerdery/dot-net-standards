# Git

Every project at the Nerdery MUST be tracked by a Version Control System (VCS).
The RECOMMENDED VCS for the Nerdery is Git.

VCS Systems MUST be listed under the Client page on Mainframe under the Systems
tab, and SHOULD be connected to a specific project.

## Ignoring Files

RECOMMENDED .gitignore:
https://www.gitignore.io/api/visualstudio,windows,macos,visualstudiocode,node,monodevelop

RECOMMENDED .gitattributes:
https://github.com/Danimoth/gitattributes/blob/master/VisualStudio.gitattributes

The goal is to keep build artifacts and developer-local settings from being kept
under source control.

This alleviates merge conflicts when pulling compiled code down, and keeps one
developer’s personal preferences for settings from beoming the defaults for
everyone.

In the general case, the nuget packages folder SHOULD NOT be kept in source
control. However, in some unique deployment scenarios or due to client
infrastructure requirements, this recommendation can be ignored on a project by
project basis. Be sure to mention it in the README so someone doesn't delete the
folder.

Feel free to build your own .gitignore using gitignore.io. Its a good idea to
include the VisualStudio, Mac, Windows, and Node presets as a baseline.

### Secrets (passwords, API tokens, etc)

See [Configuration](configuration.md).

## Branching Strategy
For vanilla git repositories (not utilizing Bitbucket, Github or Gitlab), the
git flow strategy SHOULD be used. (see
http://nvie.com/posts/a-successful-git-branching-model/).

Tools such as SourceTree and git flow command line are RECOMMENDED for ease of
use.

Git based repositories that support a pull request model (eg. Bitbucket, Github)
MAY opt to not use the git flow model, and MAY instead use the Github flow model
(see https://guides.github.com/introduction/flow/).

### Releases

All releases SHOULD be tagged in git with an appropriate release/{version} tag.
An exception MAY be made If releases are so frequent this would lead to tag
overload, such as when continuous delivery strategies are being implemented.
