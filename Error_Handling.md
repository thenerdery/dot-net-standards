Error Handling
===========================================

At the highest level, exceptions in .NET MUST NOT be surfaced to a user. All exceptions that are raised SHOULD either be handled (via a catch statement) or surfaced to a higher level. All exceptions SHOULD be logged in production if they are surfaced to the highest module (eg. WebUI, WebAPI).

Errors SHOULD produce meaningful results for a user. In the case of a website, a page or dialog box detailing an error has occurred should be presented. In the case of an API, a message without a stack trace should be presented, with an OPTIONAL error code.

Baseline Standards
-------------------------------------------
See here the MSDN article on Best Practices: https://msdn.microsoft.com/en-us/library/seyhszts(v=vs.110).aspx

### Exceptions to MSDN Recommendations
####Creating Exceptions
* Some classes MAY throw exceptions. Specifically, classes in lower modules (see Solution Structure) SHOULD throw exceptions if necessary. Additionally, they MAY define their own exception types when a .NET framework exception does not apply.
* Methods SHOULD NOT return null. Unless clearly stated, methods should return an object that will not result in a NullReferenceException in higher modules.

Connecting to the Solution Structure
-------------------------------------------
All classes created below the Services layer SHOULD NOT handle exceptions, except in the most basic of cases. The Services layer MAY handle exceptions if necessary, and any additional exceptions MUST be managed by the highest layer (WebUI / WebAPI). The highest layer SHOULD log any non-trivial exceptions.

Supporting Documentation: [Solution Structure](Solution_Structure.md)
