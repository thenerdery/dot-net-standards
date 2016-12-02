# Versioning

Any deployed projects SHOULD have a way to determine what version has been
deployed. This section aims to set standards on how to display versioning
information of your deployed project, and when/how to update it.

## Versioning Strategy

Projects will generally fall into 2 categories:

1. A project delivered to an end user that is on-demand, such as a website.
   These can be updated frequently or infrequently, however the update can be
   completely transparent to the user access it.
2. Web/C# APIs and Apps. The updates for these are typically visible (although
   they might be updated automatically in the background without the user’s
   knowledge and explicit permission)

### Web Applications

Web applications, when deployed, SHOULD publish a build or release number and/or
git hash of the version that was built. This could be something as simple as the
date of the deployment read from a file on the file system or web.config.

This can be presented on the site hidden by an HTML code comment or a custom
HTTP header, or if desired, built into the design (footer, or other place where
version information would be displayed)

### Apps and Libraries

If you are publishing a desktop application, mobile application, or C# library,
your project SHOULD abide by [Semantic Versioning](http://semver.org/).

*When creating APIs, see [API Communication](API_Communication.md) section for
properly versioning a REST API*

## Versioning Documentation

When performing semantic versioning or API Endpoint versioning, your project
SHOULD contain a Changelog. At a minimum, the changelog SHOULD be a simple
markdown file that lists the following:

1. Each version released and, if possible, a link to either the CI build of that
   version, or the deployed artifact, or a web-based git browser with the
   release tag.
2. Underneath each version, list the following:
    1. A bullet for each change that occurred.
    2. If a change is associated with a JIRA or other issue tracker related
       issue, include the link to that issue.

This does not apply to Web Application versioning.

## When to increment the version number

Version information SHOULD be incremented in anticipation of a deployment. When
the version information is incremented, the Changelog.md file SHOULD also be
updated with change information (if a changelog is being published).

If using [gitflow](git.md#branching-strategy), version numbers and changelogs
SHOULD only be updated on a release branch.

## Applying version and build metadata to binaries

All .NET project files (.csproj) SHOULD build with assembly attribute metadata
that defines the assembly version. This SHOULD be stored in
`/Properties/AssemblyInfo.cs`.

If multiple projects are used and if the projects have tight dependencies on
each other, (e.g., <Project>.Web, <Project>.Core, <Project>.Data) a single
version number SHOULD be used for all of them, such that management of the
multiple version numbers across each project does not become a burden.

In this case, the version number SHOULD be specified in a single file that is
included in each coupled project. This way you only have to bump the version
number in one place.

### MSBuild Community Tasks

Here are some tools we recommend for simplifying the process of keeping each
project on the same version without repeating yourself.

There are several MSBuild extension tasks that you can include in your .csproj
files that will allow this information to be automatically applied. This can be
used in conjunction with continuous integration tools like Bamboo or TeamCity.

See [https://github.com/loresoft/msbuildtasks/](https://github.com/loresoft/msbuildtasks/) for nuget download information and additional documentation.

See [http://farm-fresh-code.blogspot.com/2015/03/convention-driven-automatic-release.html](http://farm-fresh-code.blogspot.com/2015/03/convention-driven-automatic-release.html) for an example of how to implement.

### AppVeyor

AppVeyor has a built in feature that can apply AssemblyInfo.cs transformations
based on the build configuration file. If AppVeyor is being used for continuous
integration, this is highly recommended, but MSBuild Community Tasks MAY be used
instead.

See [https://www.appveyor.com/docs/build-configuration#assemblyinfo-patching](https://www.appveyor.com/docs/build-configuration#assemblyinfo-patching) for additional information.

