# Inversion Of Control (IOC)

Inversion Of Control is a design pattern where code receives components to
execute its functionality.  Dependency Injection (DI) is the IOC pattern that
SHOULD be used on all projects. A framework SHOULD be used to provide DI
capabilities.

## Nuget Package

### Autofac

[https://www.nuget.org/packages/Autofac](https://www.nuget.org/packages/Autofac)

The Autofac DI framework SHOULD be used on any .NET projects using IOC. While
there are numerous available libraries with similar capabilities (such as
Ninject), we prefer to stick with Autofac for consistency.

* Homepage: https://autofac.org/
* MVC Boilerplate: http://docs.autofac.org/en/latest/integration/mvc.html
* WebAPI Boilerpalte: http://docs.autofac.org/en/latest/integration/webapi.html

## Patterns to use

### Dependency Injection

Dependency Injection is a design pattern in which a class is configured with its
dependencies and collaborator objects from the outside. It does not instaniate
them itself.

This aids in future maintenance and unit testing as the implementations of
collaborating classes can be swapped out, mocked, or modified without effecting
the main class.

### Facade Pattern

The facade pattern (as it relates to IOC) is a design pattern in which a single
dependency sits between a class and multiple other collaborators to shield that
class from knowing too many implementation details.

For example: you might have a `CustomerService`, an `OrderService`, and a
`ShippingService` all involved in processing an order. To process the order,
each of these would have to be injected into the class so it could use them to
read and write data or performance other logic. When there are a large number of
collaborators, it can be helpful to wrap them behind a facade: the
`OrderProcessingService` that exposes only a couple of relevant methods ands
forwards them on to the correct backing service. Then you only needs to inject
`OrderProcessingService`.

## Anti Patterns that SHOULD be avoided

### Service Locator pattern

In the service locator pattern in which classes reach out to a well known (often
static) class and ask it for the dependencies it needs. This makes classes hard
to test because you can't easily pass in a mock or stub object instead. It also
doesn't clearly document what the class needs in order to function, and can make
it harder to port that code to other environments This makes classes hard to test because
you can't easily pass in a mock or stub object instead.

```csharp ServiceLocator.cs
public class OrderService
{
    private Database _db;

    public OrderService()
    {
        _db = ServiceLocator.GetInstance<Database>();
    }
}
```

*In a real system the service locator might not go by the name
`ServiceLocator`, it often masquerades as a "Factory" class*

In this example it's not clear from the constructor signature that the class
needs an instance of `Database` to work. It's also hard to stub in a fake
`Database` during unit tests.


### Optional dependency

The optional dependency pattern is  where a constructor with dependencies is
created but there is also another constructor that will self instantiate the
classes.

```csharp OptionalDependecy.cs
public class OptionalDependencyExample
{
    public OptionalDependencyExample(ICollaboratorService service)
    {
        _service = service;
    }

    public OptionalDependencyExample() : this(new ConcreteCollaborator())
    { }
}
```

This is marginally better than the service locator because we can stub in
dependencies during tests, but it is still hard to pick up the code and move it
to a different project that doesn't have a `ConcrenteCollaborator` available.
And if `ConcreteCollaborator` should change and require new constructor params, we
have to update this class definition and possibly all the places that use it so
that they can pass in the new parameter.

### Property injection

Property injection is where a class needs it's collaborator set on a public
property. If you forget to set the property, you may have a class that doesn't
work. If a class needs a collaborator, you SHOULD use constructor injection so
that the compiler can enforce its preconditions are met. You SHOULD always seek
to make it impossible to instantiate a class with an invalid state.

*Note: Sometimes there are framework limitations that require you to use
property injection (MVC ActionFilters spring to mind), but this pattern is
to be avoided in code you control*

## General Guidance

* Dependency configuration SHOULD be done in code modules rather than XML files
  when possible
* Using constructor injection can lead to a class having many (>5) parameters
  which is often an indication of violating the Single Repository Principle.
  The facade pattern can help with this where a single class is used to
  aggregate several dependencies into a single class. This is RECOMMENDED in
  cases where a class begins to exhibit a large number of dependencies.
* Autofac dependency configuration SHOULD be centralized as much as is possible,
  however if a project needs to maintain its own dependencies (such as a service
  level class) this SHOULD be done in a single class.  Use of Autofac
  [modules](http://autofac.readthedocs.org/en/latest/configuration/modules.html)
  can be considered for organization.
* Within an autofac configuration class areas or like objects SHOULD be grouped
  together into functions.

```
public class AutofacServiceRegistration
{
    public static ContainerBuilder RegisterServices(ContainerBuilder builder)
    {
        // Register Services
    }
}
```

 * Use `As<T>()` in Delegate Registrations

Use

```
builder.Register(c => new Component()).As<IComponent>();
```

In place of

```
builder.Register(c => (IComponent)new Component());
```

or

```
builder.Register<IComponent>(c => new Component());
```

* Register Components from Least-to-Most Specific
    * Autofac overrides component registrations by default.

* Register Frequently-Used Components with Lambdas
    * Use

```
builder.Register(c => new Component());
```

Instead of

```
builder.RegisterType<Component>();
```

As this can yield a 10x performance gain in resolve() calls however this only
makes sense in frequently access components.

References:

[http://blog.ploeh.dk/2010/02/02/RefactoringtoAggregateServices/](http://blog.ploeh.dk/2010/02/02/RefactoringtoAggregateServices/)

[http://docs.autofac.org/en/latest/best-practices/](http://docs.autofac.org/en/latest/best-practices/)

