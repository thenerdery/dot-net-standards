# Data Access Patterns

## Guidance

### Profiling

It is RECOMMENDED to use a [profiler](Profiling.md) to be aware of what your
data access layer is doing.

### Repository and Unit Of Work patterns

The Entity Framework DbContext already implements the Repository and UnitOfWork
patterns.

* Avoid layering additional repositories/unit of work on top of the DbContext.
* Developers MAY use a small wrapper around the DbContext to share common
  queries (a "repository of queries") and avoid repeating frequently used
  queries. For example, when filtering by access level or "ownership,"

```
public class Queries
{
    private readonly FooDbContext _context;

    public Queries(FooDbContext context)
    {
        _context = context;
    }

    public IQueryable<Foo> GetFoosForClient(int clientId, RoleEnum role, Guid userId)
    {
        return _context.Foos
                       .Where(f => f.ClientId == clientId
                              && (role == RoleEnum.Admin || f.UserFoos.Any(uf => uf.UserId == userId));
    }
}
```

* Follow an existing implementation where necessary/expedient. Evolve to better
  patterns as possible for on-going development.
* *Turn off tracking for read-only queries*
* Prefer when (implementation) simplicity is a critical consideration
* Mock your data context where possible

### N+1 Queries

An "N+1" query issue is when a request retrieves a collection of data models,
then performs a query per model to get additional, associated data.  This type
of query is frequently at the heart of a slow performing page.  Lazy-loading is
the underlying culprit when such a problem arises. Resolving these issues
requires understanding the performance requirements of the application and the
data actually used and balancing that against the complexity of the solution.
Generally, hiding your DbContext behind a generic repository is going to make
resolving this more difficult.

* If all of the data being retrieved is required, use the Include extension
  method on the DbSet for the model to eagerly load the related data.  Multiple
  Includes can be used if more than one association needs to be fetched.
* If a relatively small subset of the data is required, consider using a
  projection to retrieve only the required data into an anonymous (or named)
  type specific to the purpose of the query.
* Not recommended: Turn off lazy loading on the DbContext (you SHOULD be using
  EF 6 or higher) via the configuration options.

### Projections

Consider using projections to view models or anonymous types to reduce and
flatten the data you are retrieving. This is particularly true for display-only
requests. Projections can help avoid the N+1 query pattern.

### CQRS - Command-Query Responsibility Segregation

* Can be applied with or without Event Sourcing and eventual consistency
* Consider using Dapper for commands and EF for (read-only) queries.
* Use integration tests for Dapper code
* Prefer when performance is a critical consideration

### OData

Avoid OData queries against hierarchical data. If required, limit the surface
area (available loaded properties and filters)

### Migrations

Developers SHOULD NOT use migrations. Follow a database-first, code-first
strategy by designing the schema first, then reverse-engineering the model
classes (using EF Power Tools or manually) and configuration for it.

### View Models/Materialization

Developers SHOULD NOT use entity models in views, rather, map your entity models
onto view-specific models.  By doing so you decouple your view from the entity
and allow it to change at a different rate and limit the surface area which are
affected by future code changes.  Additionally, you maintain the separation of
concerns - the view models MAY be flattened representations that only the view
is concerned with and thus allow you to maintain appropriate abstractions in
each component rather than mixing abstractions to serve both uses. Finally, by
mapping to a view models you ensure that the data is retrieved from the database
before the view is rendered, again maintaining a separation of concerns. Do use
AutoMapper and maintain property name/type consistency between entities and view
models to make this easier.

### Types

Developers SHOULD use appropriate data types (enums mapped to/from int or
tinyint, boolean as bit, etc) rather than storing data as strings. If you need
to have human-readable data available in the database, use views that map the
underlying types to human-readable values rather than introduce potential data
integrity issues.