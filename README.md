Nerdery .NET Standards Specification
===========================================

Welcome to the .NET Standards Specification for [The Nerdery](http://nerdery.com).
Maybe you're a newb on your first week looking to get a lay of the land.
Or maybe you're a hopefully future Nerd trying to figure out how we tend to do things.
Or maybe you're interested in partnering with us on your next big idea.
If you want to find out more about our .NET recommendations and standards,
you've found the right place.

## Why standards

### Overhead in project switching

The .NET discipline at The Nerdery works on multiple parallel projects
spread among small teams and individuals.
As an engineer you will often be expected to jump between projects to help where needed.
There will be periods of time where you work on a single project for a long duration,
and other periods where you will help with maintenance on multiple projects concurrently.
This can be pretty exciting: you get experience in different tech and business domains,
and rarely get bored of a particular project and its associated technologies.

But it comes at a cost.

If you're constantly jumping between projects with wildly different approaches,
it can be overwhelming to be always finding your bearings.
If projects are structured differently and use different tools,
you often feel lost and confused for the first few days until you find your way.

Similarly, starting new projects can lead to "analysis paralysis"
in which lead developers spin their wheels trying to make sure they pick the best tools and practices.

Standards give us a place to make recommendations so that teams find consistency
as they move around from project to project.

### Mechanism for Feedback

Without documented standards, each team picks their own favorite tools
and has less incentive to share their findings with the rest of the discipline.
It becomes easy for information to become siloed.

Standards provide a process for sharing feedback:
if an approach recommended in the standards was detrimental to a project,
we now have a place to bring that feedback to the attention of the entire discipline.

Over time, the standards and our discipline improve in parallel.

## Consistency Principle

Having documented recommendations is fairly new. This makes it likely that you'll encounter
a project that does things a little differently.

Whenever possible, follow the consistency principle. This means that if an
existing project uses a method, framework, library, or any other development
tool or technique that does not conform to these standards, use the existing
method instead.

## Changes

These standards are a *living document*,
as evidenced by their current home on [Github](https://github.com/thenerdery/dot-net-standards).
There will be changes over time as we seek to improve our offering in service to our clients.

Major changes will be communicated internally,
but it will still be beneficial to check in frequently, or Watch the GitHub repo to stay informed.

### Contribution guidelines

* Create feature branch or fork the repo
* Submit pull request to master and cc Jonathan Dexter (@mandest), Marshall Glanzer (@NerderyMGlanzer), or Matt Burke (@akatakritos).

Feel free to use Issues to raise feedback on existing standards,
suggestions for updates, or other constructive criticism.

## Standards Categories

The standards have been edited to include specific language

 - MUST/REQUIRED
 - SHOULD
 - MAY/OPTIONAL

### MUST/REQUIRED

If a standard declares a MUST, all projects we work on will conform to that standard.
This is the only area where we would expect to push back on a client
if something does not conform (e.g.. no source control).

For your convenience, the following areas contain MUST directives:

  * [**API communication: Versioning**](API_Communication.md) — Any API communication that is planned to be updated and consumed externally MUST follow a versioning scheme (e.g.. `/v1/`) for backwards compatibility.
  * [**Configuration: Credentials**](configuration.md) — Production credentials MUST NOT be stored in source control.
  * [**Error Handling**](Error_Handling.md) — Exceptions MUST NOT be surfaced to a user in a production environment.
  * [**Source Control: Git**](git.md) — Every project MUST have source control. Git is preferred, but not required.
  * [**Logging**](Logging.md) — Personal health information (PHI) and credentials (e.g.. passwords) MUST NOT be logged. Exceptions in the error and fatal category MUST be logged. Logging needs Date Time, Severity, Location, Message.
  * [**Project Docs**](Project_Docs.md) — Each project MUST have a README.md file.
  * [**Security**](Security.md) — Various MUSTs depending on the category.
  * [**Solution Structure**](Solution_Structure.md)
    * Independent "module" MUST be a project or a folder (don't mix concerns).
    * MUST NOT use wwwroot for your app.
    * Must have your file path mirror the namespaces.
    * If a `Common` module exists, it MUST NOT depend on parent modules (e.g.. Web API).
    * Services MUST have an interface.

### SHOULD
If a standard declares a SHOULD, if at any point we are **making a design decision** (see below),
the SHOULD directive should be followed.

If we inherited code, we can expect to not change any existing implementations
to conform with our standards.
The cost of making the changes may be more than its worth.

The majority of the standards fall into this category, especially our recommended libraries.

### MAY/OPTIONAL

If a standard declares a MAY or OPTIONAL, the section in question has been vetted
to be effective in certain scenarios, and can be implemented as a choice.

Since the standards are not comprehensive, any topic not explicitly documented can be considered a MAY/OPTIONAL.

## Usage Guidelines

What standards *shouldn't* do is require us to examine all of our projects for areas of change.
Instead, the standards should be implemented in situations where the following criteria holds true:

#### Making a Design Choice

For example, if you're selecting which ORM to use for a MS SQL connection.

In this situation, any standard that has a SHOULD or higher directive needs to be followed.
If a standard with a SHOULD directive isn't followed,
you need to document the reasons in the project README.

#### Starting as Lead on an Existing Project

If you are beginning a project as a lead, any directive in a MUST category has to be assessed.

If one of these isn't being followed (say, a client emailed us a .zip file of source code),
we need to implement it.

#### Standards Exceptions: What to do if you need to deviate

Exceptions will happen.

Standards are guidelines, they aren't binding contracts.

If an exception occurs, please document the exception:

* If it's a SHOULD or RECOMMENDED: document it in the README.md when you make the design choice.
* If it's a MUST: validate the exception with a PSE. If multiple parties feel there's a good reason for an exception, document it in the README.md.

When documenting an exception to the standards keep in mind that the README.md of a project is a client deliverable
and that the audience may not have knowledge of what our standards are.
Because of that it may be a good idea document the choices that were made
without calling out what choices were decided against.

It is a good idea to document the reasons for deviating from the standards,
even if the README doesn't come right out and say "this is an exception to our standards".
An internal ramp-up/orientation document is a great place for this information.

For example:

THIS: "In this project we are using $X for $REASONS"

NOT THIS: "Although our standards are $Y, we're going to use $X here."

In the latter case, the client may wonder if they are getting sub-par work. If we had
a standard, why not follow it?

## Questions?

If you have questions, talk to your PSE.
If you'd like to get involved in the standards further, please do!

[Add an issue](https://github.com/thenerdery/dot-net-standards/issues).

### Who else can I talk to?

* Jonathan Dexter (.NET TM)
* Marshall Glanzer (Standards Taskforce Champion)
* .NET PSEs (Benevolent Oligarchy in charge of long-term maintenance)

## Full Table of Contents

| Section | Summary |
|--------:|--------|
|[API Communication](API_Communication.md)|Standards for creating and versioning web-based APIs|
|[Client Side Boilerplate](Client_Side_Boilerplate.md)|Steps for getting familiar with Nerdery Client-side boilerplate and processes as a back-end developer|
|[Code Coverage](Code_Coverage.md)|Guidance around unit test code coverage|
|[Configuration Management](configuration.md)|Addresses managing, maintaining and using credential sets and other secret information within the context of a Nerdery-managed .Net Project|
|[Data Access Patterns](Data_Access_Patterns.md)|Standards and guidance around patterns for data access|
|[Error Handling](Error_Handling.md)|Standards and guidance around error handling|
|[Source Control (Git)](git.md)|Standards for source control configuration and usage with Git|
|[Inversion Of Control (IOC)](IOC.md)|Standards and guidance around IOC pattern implementation|
|[Logging](Logging.md)|Standards and recommendations for application logging|
|[Mocks and Fakes](Mocks_and_Fakes.md)|Recommendations for Mocking frameworks|
|[Object Relational Mappers (ORMs)](ORM.md)|Standards and recommendations for database ORM frameworks|
|[Profiling](Profiling.md)|General tool recommendations for database and application performance profiling|
|[Project Docs](Project_Docs.md)|Standards and guidance for project documentation, including README and CHANGELOG documents|
|[Security](Security.md)|Security standards for the .Net developer|
|[Solution Structure](Solution_Structure.md)|Recommended solution structure for a new application|
|[Unit Testings](Unit_Testing.md)|Recommended unit testing framework|
|[Versioning](Versioning.md)|Recommendations around versions and managing version information|

## Framework and Product Table of Contents

| Section | Summary |
|--------:|--------|
|[Umbraco](CMS_Umbraco.md)|Standards, guidance, and examples for use of CMS Umbraco 6 and 7|
|[Kentico 8.2](CMS_Kentico_8_2.md)|Standards and guidance for use of CMS Kentico 8.2|


## Glossary for External Readers

If you are not a Nerdery employee, you will occasionally come across references to internal tools
and abbreviations that you might not have heard of.
Rather than cluttering up the standards with parentheticals to define terms,
we have tried to helpfully collect definitions and explanations here.

**PSE**: Principle Software Engineer

**Mainframe**: Our internal intranet tool. Includes internal documentation,
custom tools, time tracking and project management.

**Nerdery Client-Side Boilerplate**: A tool for generating a lot of the common
build tools and project structure used for front-end (HTML/CSS/JS) development
