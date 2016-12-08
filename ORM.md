# Packages+Integration: ORM

## Nuget Packages

### Entity Framework

You SHOULD use the latest
[EntityFramework](https://www.nuget.org/packages/EntityFramework/) for most
application queries, especially INSERT and UPDATE statements.

### Dapper

You MAY use [Dapper](https://www.nuget.org/packages/Dapper/) when needing to
write custom SQL for high performance areas of the application. You SHOULD NOT
use Dapper for updates and inserts as they are tedious to maintain when new
columns are added.

## Avoid:

### Straight ADO.NET

Avoid using straight ADO.NET libraries (`SqlAdapter`, `DataTable`, `SqlCommand`,
…) as they can introduce significant maintenance in keeping the SQL queries up
to date with changing models. New developers to the project will be slower at
generating ad-hoc queries as they'll have to consult documentation for column
names. The intellisense enabled by LINQ is a great efficiency boost.

### Stored Procedures

Stored procedures are difficult to maintain and migrate, and often hide important
business rules far away from the relevant application code.

If you must use stored procedures for certain performance or client requirements,
prefer Dapper for integration.

### nHibernate, other ORMs

We have a high level of institutional knowledge in using, configuring, and
tuning Entity Framework. Avoid using other ORMs that will introduce unnecessary
support burdens.

See also: [Data Access Patterns](Data_Access_Patterns.md), [Profiling](Profiling.md)

**Always, always use profiling to ensure that your queries are efficient.**

