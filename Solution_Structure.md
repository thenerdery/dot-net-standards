# Solution Structure Standards

This document details the Nerdery standard for solution structure when
creating a new application.

### Term Definitions

**Module** - the organizational unit for a functionally distinct area of a
solution.  MUST be organized in a folder or a project of it’s own.

### Solution Scope

A separate solution SHOULD be created for each deployable part of the application, for example,
if a project has multiple APIs, each API should have it's own solution. If there
is a need to share code between solutions (note: this induces an external coupling
that you should not introduce lightly), this should be done using versioned NuGet packages.

A solution MAY contain multiple, deployable components IF the number of those
components are small and complete. For example, you MAY create a solution that contains
a Web Application and an Authorization Application if those applications can
be deployed together.

### Naming

#### Solutions

Solutions SHOULD follow the naming convention: Client.Application.Component

> e.g. Acme.Explosives.OrderApi

If a Solution combines several components a more generic name MAY be used.

> e.g. Acme.Application solution that contains Web, Core, and ScheduledTasks projects.

The same name SHOULD be used for

   1. Solution Name
   2. Solution Folder Name
   3. Project Name (if a single component)
   4. Project Namespace (if a single component)
   5. Assembly Name (if a single component)
   6. Assembly File Name (if a single component)
   7. Root Namespace

#### Projects

Projects SHOULD follow the naming convention: Client.Application.Component.Module

> e.g. Acme.Explosives.OrderApi.Core

If a solution combines several components a more generic name MAY be used.

> e.g. Acme.Explosives.Core project that contains core behavior for several components.

The same name SHOULD be used for 

   1. Project Name
   2. Project Folder Name
   3. Project Namespace
   4. Assembly Name
   5. Assembly File Name

### Web Application projects

* SHOULD be created using .NET Core
* SHOULD use [feature folders](DotNetCore.md#feature-folders)
* All code SHOULD be kept within a single project structure

### Module Sub Folders

* If a project contains both interfaces and implementation these SHOULD kept together. If there is
  a single implementation for an interface they SHOULD be kept in the same file with the interface
  at the top and the implementation following it.  If there are multiple implementations, consider
  creating a folder for the module and putting the interface and implementation in it.
* .NET namespaces MUST mirror the folder structure in which classes in that
  namespace are located.  For example: `Client.Project.Data.Sql.SqlParser`
  resides on the path `Data\Sql\SqlParser.cs` within the `Client.Project`
  root.
* Migrations (see: [Data Access Patterns](Data_Access_Patterns.md))
* Docs (see: [Project Documentation](Project_Docs.md))

## Solution Structure

The general code structure MAY consist of the following modules. These
SHOULD be handled as areas/folders in your solution until they need
to be shared with additional components of your solution. For larger
solutions containing multiple deployable components, you MAY start by
creating them as separate projects.

This is not an exhaustive list. This is a list of items that are
present in common applications. If additional project types arise that do not
fit in one of these categories, additional folders and/or projects MAY be created.

### Common Modules

The modules below are listed in order of dependency, modules MUST NOT depend on
a module listed below it.  In the case where modules may reside in the same
project care MUST be taken to maintain this rule.  For separate projects a
reference MUST NOT be made that violates this rule. Each module is detailed
below.

* Core
* Domain
* Data
* Services
* *Api* - a suitably named API, ex. OrdersApi, ProductsApi
* Web

**Project leads SHOULD enforce dependency constraints with a tool
such as [NetArchTest](https://github.com/BenMorris/NetArchTest)
[(pkg)](https://www.nuget.org/packages/NetArchTest.Rules/) 
in a unit test project.**

#### Example Structure: Small Solution with multi-module project

* Project: Client.*Application*
   * Folder: Core
   * Folder: Domain
   * Folder: Data
* Project: Client.*Application*.Test
   * Folder: Core
   * Folder: Domain
   * Folder: Data

#### Example Structure: Large Solution with single-module projects

* Project: Client.*Application*.Domain
* Project: Client.*Application*.Domain.Test
* Project: Client.*Application*.Core
* Project: Client.*Application*.Core.Test

### Client.*Application*.Core

The `Core` namespace SHOULD contain all functionality that is not expansive
enough to warrant a project of it’s own, but are required by multiple consumers
(e.g. *Application* and *API*).       

* Logging
   * Generic helper classes for logging
* Profiling
   * Generic helper classes for profiling
* .NET Framework Extensions, Reflection Helpers
   * Items expanding core functionality of the .NET framework.
   * Extension methods

### Client.*Application*.Domain

* Domain Objects (POCO) implementing the terms of art for the solution
* Business logic scoped only to the above objects
   * Validators
   * Filters
   * Factories
   * Regular Expressions
* Shared data transfer objects

### Client.*Application*.Data

* Reference to persistence libraries
* Entity Framework Fluent API mapping of Domain objects
* Clients for APIs

See Also

* [Data Access Patterns](Data_Access_Patterns.md)
* [ORM](ORM.md)

### Client.*Application*.Services

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

### Client.*Application*.*Api*

The *API* module MAY or MAY NOT exist, depending on the application.

If the application needs to support external users of the data and services,
an *API* SHOULD be used. If you have multiple APIs you *SHOULD* implement each in a separate
solution so that it is independently deployable. See the standards on [API Communication](API_Communication.md)
for more information.

*API* MAY be used for AJAX.

* API for external consumers
   * It is RECOMMENDED that the API be RESTful
* (optional) AJAX calls

### Client.*Application*.Web

* UI (Razor)
* Authentication
* Configuration

#### Web Project Structure

* Assets - for .NET Core this should be in *wwwroot*
* Attributes
* Features (you MAY omit the subfolders)
  * Controllers
  * Models
  * Views
* Docs
* Logging
* Models (shared models)
* Resources
* Util
* Views (shared views)
   * Shared

*Web* and *API* MAY be combined into a single project for smaller applications
or for applications where the hosting environment is limited (e.g. A single
azure machine is needed to host both Web and *API* sites).

### Module Testing

* Module tests MUST reside in an organizational unit (folder or project)
  mirroring the module to be tested.  The project containing unit tests MUST use a
  namespace ending in "Tests".  The project containing integration tests MUST use
  a namespace ending in "IntegrationTests".
* Test projects MUST NOT contain a mixture of unit and integration tests. Mixing test
  types makes it difficult to use these tests at different stages of the CI/CD pipeline.

See the standards on [unit testing](Unit_Testing.md) for more information.


References
* http://mikehadlow.blogspot.com/2007/07/how-to-structure-visual-studio.html
* https://msdn.microsoft.com/en-us/library/ee817674(pandp.10).aspx
