REST API Communication
===========================================

## API Consumption

When creating code that will consume a REST API, The library RestSharp SHOULD be used.

[http://restsharp.org/](http://restsharp.org/)

Any sensitive API keys or necessary information to protect security SHOULD use proper configuration management techniques

## Framework

For creating REST APIs, the ASP.Net Web API framework SHOULD be used.

## Authorization

This will largely depend on the specific project requirements, but in general, for authorized access to the API, in general:

* Server-to-server: For use cases where a server or known system needs to communicate with our API, keyed-hash message authentication code (HMAC) SHOULD be used.  See [http://bitoftech.net/2014/12/15/secure-asp-net-web-api-using-api-key-authentication-hmac-authentication/](http://bitoftech.net/2014/12/15/secure-asp-net-web-api-using-api-key-authentication-hmac-authentication/) for an example.

* Client-to-Server: For use cases where a client user (such as a browser or app) needs to communicate with the API, OAUTH or other authentication methods SHOULD be implemented for access.

## Definition and Discovery

One of the biggest missing pieces in REST when compared to other HTTP API methods such as SOAP, is discoverability. Documentation for REST APIs SHOULD take place in the form of a webpage. For this, we recommend the use of Swagger / OpenAPI initiative. ([http://swagger.io/](http://swagger.io/)) For .Net REST Web API projects, we recommend the use of Swashbuckle ([https://github.com/domaindrivendev/Swashbuckle](https://github.com/domaindrivendev/Swashbuckle))

If the intended consumer of the API is the general public, or some potentially unknown consumers in the future (e.g., not just 1 client ever), Full documentation, including some simple examples, SHOULD be included for all endpoints that are created.

For internal developers or client developers, basic documentation including endpoints are required. Full descriptions and writeups are not necessary unless not obvious based on usage.

## Versioning

To ensure proper servicing of multiple clients that may be using API endpoints we wish to modify or deprecate, API endpoints must begin from a Base URL that contains a version identifier. This allows APIs to remain backward compatible when publishing updates.

When modifying the API spec that would bring in a breaking change, the version number of the API must be updated. Previous versions of the API must remain operational. Your project’s code architecture SHOULD account for this change to take place at some point in the future, but API versions SHOULD not be updated frequently, if ever. Ideally you will only ever have 1 API endpoint that you update with only forward-non-breaking changes. However, once you do have a breaking change and have consumers of a production endpoint, you must add a new version.

e.g., `https://<host>/api/v1/<resource path>`, `https://<host>/api/v2/<resource path>`

## Content Type

JSON SHOULD be the content type published by your API. The Newtonsoft.JSON serializer that ships with Web API SHOULD be used.

