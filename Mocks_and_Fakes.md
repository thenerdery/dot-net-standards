# Mocks & Fakes

## Nuget Packages

### Moq

For standard .NET projects, you SHOULD use
[Moq](https://www.nuget.org/packages/Moq/).

[FakeItEasy](https://www.nuget.org/packages/FakeItEasy/) is also common in
existing projects within the Nerdery, but for consistancy we are recommending
Moq for new development.

### PCLMock

Moq does not support PCL projects that only expose a subset of the .NET base
class library. You SHOULD use [PCLMock](https://github.com/kentcb/PCLMock) in
these cases instead.


## Guidance

Whether you're following a TDD methodology or just adding some tests around
existing code, Mocks SHOULD be used to test dependencies the the unit under
test.

### Handrolled Mocks

If a mocking library is not sufficient, or if the functionality provided would
be too complicated to setup with a mocking framework, (this is rare, but
happens) mocks MAY be created by hand.

### Third Party Services

For Unit Tests, you SHOULD mock any service that interacts with a third party,
such as a database or API.

### Deep Mocking

If you find yourself writing a mock that returns a mock, that's a design smell
indicating your class knows too much about its collaborators. You SHOULD NOT write
such a mock, though its sometimes necessary when trying to test code that deals
with a third party library or framework.

### Too many mocks

If you find yourself writing more than 3 mocks for a class, that's a design
smell indicating your class does too much and is violating the single
responsibility principle. You SHOULD attempt to refactor to a composite service
or facade in these cases.