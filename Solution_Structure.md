# Solution Structure Standards

This document details the Nerdery standard for folder/project structure when
creating a new MVC application.

### Term Definitions

**Module** - the organizational unit for a functionally distinct area of a
solution.  MUST be organized in a folder or a project of it’s own.

### Project Naming

Projects SHOULD follow the naming convention: Client.Application.Project

> e.g. Acme.Explosives.WebAPI

If a project combines several modules a more generic name MAY be used.

> e.g. Acme.Application project that contains Core, Domain, and Data modules.

The same name SHOULD be used for

   1. Project Name
   2. Project Folder Name
   3. Assembly Name
   4. Assembly File Name
   5. Root Namespace

### Web Application projects

* MUST use virtual folders and MUST NOT use wwwroot
* All code SHOULD be kept within a single project structure

### Module Sub Folders

* If a project contains both interfaces and implementation these SHOULD be
  separated by creating an “Implementation” folder
* .NET namespaces MUST mirror the folder structure in which classes in that
  namespace are located.  For example: `Client.Project.Data.Helpers.SqlParser`
  resides on the path `Data\Helpers\SqlParser.cs` within the `Client.Project`
  root.
* Migrations (see: [Data Access Patterns](Data_Access_Patterns.md))
* Docs (see: [Project Documentation](Project_Docs.md))

## Solution Structure

The general code structure SHOULD consist of the following sections.  For larger
projects each of these sections SHOULD be a separate project, for smaller
projects these sections MAY be areas/subfolders inside of a single project.

This is not an exhaustive list. This is a minimal amount of items that are
present in common applications. If additional project types arise that do not
fit in one of these categories, folders and/or projects MAY be created.

### Common Modules

The modules below are listed in order of dependency, modules MUST NOT depend on
a module listed below it.  In the case where modules may reside in the same
project care MUST be taken to maintain this rule.  For separate projects a
reference MUST NOT be made that violates this rule. Each module is detailed
below.

* Core
* Domain
* Data
* DomainServices
* WebAPI
* WebUI

#### Example Structure: Small Solution with multi-module project

* Project: Client.Application
   * Folder: Core
   * Folder: Domain
   * Folder: Data
* Project: Client.Application.Test
   * Folder: Core
   * Folder: Domain
   * Folder: Data

#### Example Structure: Large Solution with single-module projects

* Project: Client.Application.Domain
* Project: Client.Application.Domain.Test
* Project: Client.Application.Core
* Project: Client.Application.Core.Test

### Client.Application.Core

The `Core` namespace SHOULD contain all functionality that is not expansive
enough to warrant a project of it’s own, but are required by multiple consumers
(e.g. WebUI and WebAPI).

* Logging
   * Generic helper classes for logging
* Profiling
   * Generic helper classes for profiling
* .NET Framework Extensions, Reflection Helpers
   * Items expanding core functionality of the .NET framework.
   * Extension methods

### Client.Application.Domain

* Domain Objects (POCO) implementing the terms of art for the solution
* Business logic scoped only to the above objects
   * Validators
   * Filters
   * Factories
   * Regular Expressions
* Shared data transfer objects

### Client.Application.Data

* Reference to persistence libraries
* Entity Framework Fluent API mapping of Domain objects
* Clients for APIs

See Also

* [Data Access Patterns](Data_Access_Patterns.md)
* [ORM](ORM.md)

### Client.Application.Services

The `Services` module contains business logic for the application. This includes
consumption of lower layers (e.g. `Data` / `Domain`), as well as custom logic for
other portions of the application to use. Services SHOULD be the main area where
business logic appears. Services SHOULD also be the lowest level in which
outside integrations occur.

This module MAY be split into more than one project or module for large
solutions, dependency management, or to facilitate multiple developer teams.

Each service class within the Services module MUST have an interface.

* Business logic
* Integrations with external services
* Connection of lower components
   * Examples may be: thin layer for retrieving and storing Data objects (Query object)

### Client.Application.WebAPI

The Web API module MAY or MAY NOT exist, depending on the application.

If the application needs to support external users of the data and services,
WebAPI SHOULD be used. See the standards on [API Communication](API_Communication.md)
for more information.

WebAPI MAY be used for AJAX.

* API for external consumers
   * It is RECOMMENDED that the API be RESTful
* (optional) AJAX calls

### Client.Application.WebUI

* UI (Razor)
* Authentication
* Configuration

#### WebUI Project Structure

* Assets
* Attributes
* Controllers
* Docs
* Logging
* Models
* Resources
* Util
* Views
   * SectionName (e.g. Account)
   * Shared

WebUi and WebAPI MAY be combined into a single project for smaller applications
or for applications where the hosting environment is limited (e.g. A single
azure machine is needed to host both UI and API sites).

### Module Testing

* Module tests MUST reside in an organizational unit (folder or project)
  mirroring the module to be tested.  The project containing tests MUST use a
  namespace ending in "Test".
* If a test project contains both unit and integration tests these SHOULD be
  placed in separate folders "unit" and "integration".

See the standards on [unit testing](Unit_Testing.md) for more information.


References
* http://london.sierrabravo.net/~mglanzer/dotnetboilerplatesetup/mdwiki.html#!organization.md
* http://mikehadlow.blogspot.com/2007/07/how-to-structure-visual-studio.html
* https://msdn.microsoft.com/en-us/library/ee817674(pandp.10).aspx
