Mocks & Fakes
===========================================

## Nuget Packages
* Recommended Package: Moq - for Non-PCL Projects
* Recommended Package: PCLMock - For PCL Projects

When developing unit tests, either via TDD or just adding additional tests to your already existing code, Mocks should always be used to test dependencies to your code being tested. If a Mocking library is not sufficient, or if the functionality provided would be too complicated to setup with a mocking framework, (this is rare, but happens) mocks MAY be created by hand.

