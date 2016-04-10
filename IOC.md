Inversion Of Control (IOC)
===========================================

Inversion Of Control is a design pattern where code receives components to execute its functionality.  Dependency Injection (DI) is the IOC pattern that SHOULD be used on all projects.  A  framework SHOULD be used to provide DI capabilities.

## Nuget Package

AutoFac 

[https://www.nuget.org/packages/Autofac](https://www.nuget.org/packages/Autofac)

The AutoFac DI framework SHOULD be used on any .NET projects using IOC.

## Patterns to use

* Dependency Injection

* Facade Pattern

## Anti Patterns that SHOULD be avoided

* Service Locator pattern - creating factory classes moves specific dependencies into separate classes but those classes could still be spread out among the codebase.

* Optional dependency - this is where a constructor with dependencies is created but there is also another constructor that will self instantiate the classes.

* Property injection - constructor injection SHOULD be used instead.

## General Guidance

* Dependency configuration SHOULD be done in code modules rather than XML files when possible

* Using constructor injection can lead to a class having many (>5) parameters which is often an indication of violating the Single Repository Principle.  The facade pattern can help with this where a single class is used to aggregate several dependencies into a single class. This is RECOMMENDED in cases where a class begins to exhibit a large number of dependencies.

* Autofac dependency configuration SHOULD be centralized as much as is possible, however if a project needs to maintain its own dependencies (such as a service level class) this should be done in a single class.  Use of Autofac [modules](http://autofac.readthedocs.org/en/latest/configuration/modules.html) SHOULD be considered for organization.

* Within an autofac configuration class areas or like objects SHOULD be grouped together into functions.

public class AutofacServiceRegistration

{

public static ContainerBuilder RegisterServices(ContainerBuilder builder)

{

}

}

* Use As<T>() in Delegate Registrations

Use 

builder.Register(c => new Component()).As<IComponent>();

In place of
	builder.Register(c => (IComponent)new Component());

or
	builder.Register<IComponent>(c => new Component());

* Register Components from Least-to-Most Specific

    * Autofac overrides component registrations by default.

* Register Frequently-Used Components with Lambdas

    * Use 

builder.Register(c => new Component());

Instead of

builder.RegisterType<Component>();
As this can yield a 10x performance gain in resolve() calls however this only makes sense in frequently access components.

References:

[http://blog.ploeh.dk/2010/02/02/RefactoringtoAggregateServices/](http://blog.ploeh.dk/2010/02/02/RefactoringtoAggregateServices/)

[http://docs.autofac.org/en/latest/best-practices/](http://docs.autofac.org/en/latest/best-practices/)

