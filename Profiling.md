# Profilers

## Why Profile?

Profiling is used to identify runtime issues in an application, this is usually
performance related but can also be helpful for troubleshooting threading issues
and identifying duplicate database queries or N+1 bugs.

## MVC

These inject a minimal UI into a web page with diagnostic information about the
request, and ajax requests.

The most comprehensive MVC profiling tool is [Glimpse](http://getglimpse.com/).
It’s RECOMMENDED to install all relevant packages for your application,
especially those for ADO/Entity Framework.  The database package helps identify
query duplicates, count, and performance for a request.

To profile specific portions of your application you MAY OPTIONALLY add
[Miniprofiler](http://miniprofiler.com/) for the ability to wrap an execution
block with profiler step diagnostics.  These are using blocks that feed timing
information into the profile view.

## General ASP.NET

The [Prefix](http://www.prefix.io/) profiling tool is a recent discovery,
developers might use it to take advantage of it’s ‘always on’, diagnostics
aggregation, and visualization.  It’s a new tool (to us) so let the community
know how you liked it.

## General .NET

[DotTrace](https://www.jetbrains.com/profiler/) is a favorite profiling tool
that works with any .NET application.  It gives very detailed thread dump
analysis and can also diff profiling snapshots to identify the effects of code
changes.

