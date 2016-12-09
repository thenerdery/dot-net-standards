# Profilers

## Why Profile?

Profiling is used to identify runtime issues in an application. This is usually
performance related but can also be helpful for troubleshooting threading issues
and identifying duplicate database queries or N+1 bugs.

## ASP.NET MVC

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

As profiling can be taxing on the system, you SHOULD include some mechanism to
only turn on profiling in production for a limited time. For example, only
administrators have the profiler running, or the presence of a cookie turns it
on and off. Otherwise don't run the profiler in production at all.

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

## Benchmarking

Occasionally you may need to run benchmarks to ensure parts of the system, or
proposed algorithms meet performance expectations. You SHOULD use
[BenchmarkDotNet](https://github.com/dotnet/BenchmarkDotNet) or another
benchmarking library rather than rolling your own. Frameworks take care of
important considerations like just in time compilation and the precision of
measurements.

Be careful not to spend too much time on benchmarks that focus on code samples
that are too small. For example, in most web applications, the database is going
to be your biggest source of latency. The microseconds of difference between two
low-level algorithms makes no difference on the other side of a 25ms query.


## General Advice

**JIT**

The first time a piece of .NET code runs, it has to be translated to native
machine code by the Just In Time (JIT) compiler. This process can be expensive
and can make the first measurement an order of magnitude or more slower than
subsequent measurements.

Disregard the first measurement.

**Caching**

Similarly, systems that make heavy use of caching will be have very different
performance characteristics during the first run or web request. While that
information is useful, most users will more frequently see a primed cache, so
take that first measurement with a grain of salt.

**System.Stopwatch**

Don't run your own profiling using `System.DateTime`. The system clock is not
precise enough for meaningful measurements. Use a high precision timer like
`System.Stopwatch`.

**Profile in Release Mode**

Debug builds are not optimized: they use a simple transformation from C# to MSIL
that makes the debugging experience easier to follow, and are therefore not a
good indication of how a system will behave in production.
