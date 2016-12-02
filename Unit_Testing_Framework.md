# Unit Testing Framework

## Nuget Packages

RECOMMENDED Library: xunit.net

We prefer xunit.net because it supports better test isolation, parallel
tests, improved assertions, and appears to be the standard for the new ASP.NET
Core.  It has broad support in CI systems.

Plugins are available for Visual Studio and Resharper to discover and run
Xunit.net tests.

NUnit and MSTest are also acceptable.

## Why Unit Tests

- Guide design of components
- Ensure correct behavior
- Guard against regressions
- Provide living documentation on using components

## Does my project need tests?

Yes.

## Conventions

Test structure SHOULD follow the same namespace pattern as the classes it is
testing. For example, when testing `Acme.Core.StringExtensions`, the tests would
be in the `Acme.Tests.Core.StringExtensionTests` class. Since namespaces define
folder structure, the tests are easy to find

Individual tests SHOULD follow an Arrange -> Act -> Assert pattern. In the first
block of the test you arrange the state you need, such as configuring mocks or
data. In the middle block of code, you exercuse the subject under test. And in
the last block you make an assertion about the result of the operation.

Some developers prefer to use `Snake_Case` for test names. You MAY break from
typical naming conventions in unit test method names.

## Unit vs. Integration Tests

Unit tests SHOULD primarily focus on exercising the behavior of a single class.
Collaborating classes are generally mocked or faked. Using proper [inversion of control](IOC,md)
will help make sure you have a seam to isolate the subject under test. Unit tests
SHOULD NOT connect to external or out of process services like databases or APIS.

Integration tests, on the otherhand, exercise larger slices of the application.
They often do indeed connect to databases or third party services and do not use
any significant mocking.

Since integration tests tend to be slower to run and require more heavy-weight
setup, you SHOULD prefer more unit tests than integration tests. Integration
tests SHOULD be kept in a separate class library project (preferabe) or
namespace to make it easier to exclude them from manual test runs in Visual
Studio.

```
MyProject.Tests
    UnitTests
        Core
            StringExtensionTests.cs
    IntegrationTests
        Data
            CustomerQueryTests.cs
```

*In this example its easy to right click on the UnitTests folder and run just
those tests.*

You SHOULD aim to make integration tests isolated. The database state SHOULD be
started from a known initial state at the beginning of each test run, preferably
in code or easy to find fixture files. You SHOULD NOT use a shared database for
integration tests unless your tests are structured in a way that concurrently
running tests are isolated from each other.

## What should be tested

The scope of testing depends a lot on the project and its budget. On faster, low
budget, quick turn around projects, it's often in you and your client's best
interest to focus on happy path integration tests that excersize the entire
system.

On larger, longer-term projects that will have many developers over many months,
with the possibility for frequent future change, you SHOULD focus more on
individual unit tests, especially in core components.

## Related Links

 - [Mocks and Fakes](Mocks_and_Fakes.md)
 - [Inversion of Control](IOC.md)