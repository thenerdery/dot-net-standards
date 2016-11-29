# Packages+Integration: ORM

## Nuget Packages

* Recommended package: [EntityFramework](https://www.nuget.org/packages/EntityFramework/)
* Recommended package: [Dapper](https://www.nuget.org/packages/Dapper/)

### Avoid:

* ADO.NET (SqlAdapter, DataTable, SqlCommand, …)
* Stored Procedures, except with Dapper
* nHibernate, other ORMs

See also: [Data Access Patterns](Data_Access_Patterns.md), [Profiling](Profiling.md)

Comments:

Our goal is not to make a determination on the qualities of various ORMs
relative to one another. Some people might feel that the choices are not
optimal, however, the goal is consistency and the ability to interchange
developers across projects. The selected ORMs are the most widely used across
the Nerdery and provide a good mix of ease of use (EF) and performance (Dapper).

**Always, always use profiling to ensure that your queries are efficient.**

