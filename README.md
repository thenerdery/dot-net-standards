Nerdery .NET Standards Specification
===========================================

Consistency Principle
-------------------------------------------
Whenever possible, follow the consistency principle. This means that if an existing project uses a method, framework, library, or any other development tool or technique that does not conform to these standards, use the existing method instead.

## How to use the Standards

The .NET discipline has completed its latest iteration in its quest to consistently improve our offering — the new .NET standards. These standards are a *living document*, as evidenced by their current home on [Bitbucket](https://git.nerdery.com/projects/BRAVO/repos/dot-net-standards/browse).

The standards have been edited to include specific language: MUST/REQUIRED, SHOULD, MAY/OPTIONAL.

### Standards Categories

* **MUST/REQUIRED**: If a standard declares a MUST, all projects we work on are likely to end up in a state that conforms to this. This is the only area where we would expect to possibly push back on a client if something does not conform to one of our standards (eg. no source control). For your convenience, the following areas contain MUST directives:
  * [**API communication: Versioning**](API_Communication.md) — Any API communication that is planned to be updated and consumed externally MUST follow a versioning scheme (eg. `/v1/`) for backwards compatibility.
  * **Configuration: Credentials** — Credentials MUST NOT be stored in source control.
  * **Error Handling** — Exceptions MUST NOT be surfaced to a user in a production environment.
  * **Source Control: Git** — Every project MUST have source control. Git is preferred, but not required.
  * **Logging** — Personal health information (PHI) and credentials (eg. passwords) MUST NOT be logged. Exceptions in the error and fatal category MUST be logged. Logging needs Date Time, Severity, Location, Message.
  * **Project Docs** — Each project MUST have a README.md file.
  * **Security** — Various MUSTs depending on the category.
  * **Solution Structure**
    * Independent "module" MUST be a project or a folder (don't mix concerns).
    * MUST NOT use wwwroot for your app.
    * Must have path mirror namespace.
    * If a `Common` module exists, it MUST NOT depend on parent modules (eg. Web API).
    * Services MUST have an interface.
* **SHOULD**: If a standard declares a SHOULD, if at any point we are **making a design decision**, the SHOULD directive should be followed. If we inherited code, we can expect to not change any existing implementations to conform with our shoulds. The majority of the standards fall into this category, especially our recommended libraries.
* **MAY/OPTIONAL**: If a standard declares a MAY or OPTIONAL, the section in question has been vetted to be effective in certain scenarios, and can be implemented as a choice.

Anything not explicitly as part of the standards can be considered a MAY/OPTIONAL at this point, excepting those cases that already have MUST/REQUIRED/SHOULD assigned to them (eg. don't choose to implement NHibernate when we have recommended EF and/or Dapper).

### Standards: When to use 'em

The standards are a thing that's out there. What it *shouldn't* do is require us to examine all of our projects for areas of change. Instead, the standards should be implemented in situations where the following criteria holds true:

#### Making a Design Choice

If you are on a project and you are **making a design choice**. For example, if you're selecting which ORM to use for a MS SQL connection. In this situation, any standard that has a SHOULD or higher directive should be followed. If a standard with a SHOULD directive isn't followed, document the reason in the README.md.

#### Starting as Lead on an Existing Project

If you are beginning a project as a lead, any directive in a MUST category should be assessed. If one of these isn't being followed (say, a client emailed us a .zip file of source code), we should implement it.

#### Exceptions

Exceptions will happen. Standards are standards, they aren't binding contracts. If an exception occurs, please document the exception:

* If it's a SHOULD: document it in the README.md when you make the design choice.
* If it's a MUST: validate the exception with a PSE or another PSE. If there's multiple parties feel there's a good reason for an exception, document it in the README.md.

### Contribution guidelines

* Create feature branch or fork the repo
* Submit pull request to master add jdexter and mglanzer to reviewers.
* After at least one approval is granted the updates can be merged

(We're working out permissions for merges, for now a few admins will do the merging)

### Who do I talk to?

* Jonathan Dexter (.NET TM)
* Marshall Glanzer (Standards Taskforce Champion)
* .NET PSEs (Benevolent Olagarchy in charge of long-term maintenance)

### Questions?

If you have questions, talk to your PSE. If you'd like to get involved in the standards further, please do! [Add a ticket](https://issues.nerdery.com/projects/DOTNET). Make sure to assign the ticket to the "Standards" component (second tab), or fork the code.
