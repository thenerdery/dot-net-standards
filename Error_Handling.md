# Error Handling

At the highest level, exceptions in .NET MUST NOT be shown directly to a user.
All exceptions that are raised SHOULD either be handled (via a `catch`
statement) or allowed to bubble up to a higher level. All exceptions SHOULD be
logged in production if they make it up to the highest module (e.g.. WebUI,
WebAPI).

See [Solution Structure](Solution_Structure.md) for more details on module
hierarchy.

Errors SHOULD produce meaningful messages for a user. In the case of a website,
a page or dialog box detailing an error has occurred SHOULD be presented. In the
case of an API, a message without a stack trace SHOULD be presented, with an
OPTIONAL error code.

## Baseline Standards

See here the MSDN article on Best Practices:
https://msdn.microsoft.com/en-us/library/seyhszts(v=vs.110).aspx

### Exceptions to MSDN Recommendations

#### Creating Exceptions

* Some classes MAY throw exceptions. Specifically, classes in lower modules (see
  Solution Structure) SHOULD throw exceptions if necessary. Additionally, they
  MAY define their own exception types when a .NET framework exception does not
  apply.
* Methods SHOULD NOT return null. Unless clearly stated, methods SHOULD return
  an object that will not result in a `NullReferenceException` in higher modules,
  if they can't for some reason, they should throw an exception. If failure is
  a common or expected situation, consider returning some kind of `Result` object.

## Connecting to the Solution Structure

All classes created below the Services layer SHOULD NOT handle exceptions,
except in the most basic of cases. The Services layer MAY handle exceptions if
necessary, and any additional exceptions MUST be managed by the highest layer
(WebUI / WebAPI). The highest layer SHOULD log all exceptions that make it that
high. If that exception is trivial, it should be handled at a lower level.
