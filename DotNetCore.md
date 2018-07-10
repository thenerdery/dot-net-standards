## Project Structure

### Core / Utility / Services Projects

Instead of where you would normally use the Visual Stuiod Class Library
template, you should instead create a .NET Standard Class Library. .NET
Standards define the set of APIs that will be available within the base class
library. For project specific libraries, go ahead and pick the latest version of
.NET Standard.

.NET Standard is needed for sharing library code with both .NET Core
applications and Full Framework applications. For example, .NET Core is not
fully baked for Azure Functions, and so you might need a full framework Function
that includes code from the shared domain project.

In fact, going forward, it's probably wise if this layer of the application
targets .NET standard even within full framework projects, to ease the burden of
a future migration.

*If you are creating a truly generic library, one which might be used across
multiple clients, projects and runtimes (Xamarin, etc), you should instead
target the lowest .NET Standard version that defines the base class library
features you will need. This ensures maximum compatibility with the many
consumers your library might have.*

## MVC Structure

### Feature Folders
Since ASP.NET Core provides additional flexibility in arranging the MVC layer of
your application, we are recommending the use of Feature Folders. Instead of
organizing files around type (Models, Views, Controllers) a better organization
would be around “feature”, whatever that is for your application. For example
you might have the Auth feature, the Home feature, the Products feature, etc.
WIthin each of these folders, you have all the required views, view models, and
controllers. Everything is easy to find and located nearby.

You can use the https://github.com/OdeToCode/AddFeatureFolders nuget package to
configure the framework to find your views.

If you have an admin section, consider making that an area, with its own set of
features. The Admin area, would have sub folders for the User Management,
Product Management features, etc.

### Other Differences from Standard MVC

The `wwwroot` folder is where .NET Core expects static files like images, CSS, and
JS to be stored. IIS/Kestrel will (by default) only serve static files from this
subdirectory.

## Configuration

Configuration takes place in `Program.cs` and `Startup.cs`.

`Program.cs` is for the highest level configuration: spawning a web host, and
maybe logging configuration if you want to be able to capture errors during the
host initialization

`Startup.cs` is for configuring application specific concerns: configuring MVC,
database connections, libraries, and dependency injection.

For complex configuration, use custom extension methods to make the main
configuration block easier to follow. See the example for setting up logging
below.

## Dependency Injection

We are recommending the default dependency injector provided by the MVC Core framework.

It supports all the most common use cases. If you need something that it can’t
do out of the box, install and configure Autofac as we would in a full framework
project

## Logging

The provided logging infrastructure in .NET core supports structured logging. In
other words, it doesn’t just pass a string down to the logging library, but
rather a template and a bag of data. Specialized logging frameworks can store
that log data in searchable and filterable ways.

For example, if you inject an `ILogger<AuthController>` to your
`AuthController`, you can log messages such as

```cs
_logger.LogInformation("User {username} logged in", model.Username);
```

Notice the template string is *not* using C# string interpolation.

Additionally, the logging framework receives both the template and the list of
parameters, and can attach the parameter value to the name from the string. If
it were configured to log to json, for example, it might write out a line such
as

```js
{"message": "User mburke logged in", "data": { "username": "mburke" }
```

Specialized tools can filter and aggregate this data to run reports (for example
Seq, Logz.io, ApplicationInsights, and many others). You could then use your
logs to answer business questions like "how many signups did we have today".

The template string approach also has the benefit that if that logging level is
disabled, no string interpolation or allocation is performed.


### Logging Library

We recommend the use of [Serilog](https://serilog.net/) as the backing store.

Serilog can write structured log events to files, databases, and a host of third
party services through plugins called “sinks”.

Log4net, which has been our recommendation for full framework, doesn’t do
structured logging.

## Credential Management

We recommend the use of the `AddUserSecrets` extension for ASP.MET Core
configuration. This extension merges in settings from  a json config file stored
on the developers local data folder, outside of the project solution. This
prevents the file getting accidentally committed into source control.

All priveledged settings should be stored in the users secrets file (passwords,
API keys, connection strings, etc).

The nuget packages is installed by default and used by `WebHost.CreateDefaultBuilder`.

When installed, you can right click on the project and Manage User Secrets. VS
will open the json file for you to edit. The file is stored in your `%AppData%`
folder. In development mode, asp.net core knows how to find and merge the
settings from this file.

https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-2.1&tabs=windows

In deployed environments, privileged configuration values can be provided in a
number of ways:

* Manual / automated build step that creates a settings.production.json file in
  the root of the project
* Environment variables
* Azure app settings (recommended for all Azure projects)

## Authentication

If you’re going to use ASP.NET Core Identity for authentication, its best to set
it up at the beginning of the project by selecting the authentication type
during the new project wizard. It can be difficult to get the ASP.NET Core
Identity migrations to run against a database that was already created and has
application data in it.

If you need to add Identity to an existing project, you might have luck using
this script to create the tables. It worked in March of 2018, but given the pace
of change in ASP.NET core its continued operation is not guaranteed.

https://gist.github.com/akatakritos/96b0c3136f8498246fa810d393927f04

Another way to create a schema is to create a temporary web project configured
with ASP.NET Core Identity and let it run its migrations against a blank
database. You can then use SQL Management Studio’s generate scripts command to
create a schema file. Keep this file in source control!

##Unit Testing

xUnit is the preferred framework for .net core.

Keep unit tests and integration tests in separate projects so it's easy to only
run one kind at a time.

### Integration Tests

ASP.NET core has new mechanisms to help spin up a test web server for writing
unit tests against the entire stack. These integration tests are good for
testing the connections between all your components. They can connect to real databases
and data sources.

Since they are slower and more brittle, you should keep the number of
integration tests low. Only write enought to try a few happy paths to make sure
components are wired up correctly. Use smaller, focused, unit tests to test
other scenarios.

1. Add a NetCore Testing project template. This adds xunit nuget and the
   Micrisoft.Net.Test.Sdk package which makes the tests discoverable by Visual
   Studio (even without R#)
2. Edit the .csproj to `<Project Sdk="Microsoft.NET.Sdk.Web">`
3. Make sure your integration test, web, and shared projets all target the same
   version of .NET Core
4. Add the `Microsoft.AspNetCore.Mvc.Testing` nuget package
5. Declare your test class to implement
   `IClassFixture<WebApplicationFactory<YourApp.Web.Startup>>` and to inject an
   instance of `WebApplicationFactory<YourApp.Web.Startup>` to its constructor.
   This is an xunit feature for shared test fixtures.
6. You may have to tweak your `Program` class to have a `public static
   IWebHostBuilder CreateWebHostBuilder(string[] args)` method, depending on
   what template VS used when you created your project. Newer proejcts will have
   this done for you. The test server infrastructure looks for this method to
   configure itself. If you use the starter files below, this is done for you,
   but if youre working on an older project it might have a different syntax in
   `Program.cs`
7. Inside each test, use the `WebApplicationFactory` object to create an
   `HttpClient` preconfigured for working with your app. For testing APIs, you
   can deserialize the JSON and inspect the data. For testing web apps, you can
   use the [AngleSharp](https://github.com/AngleSharp/AngleSharp) package to
   inspect the HTML DOM in C#. A utility method like [the one in this
   project](https://github.com/aspnet/Docs/blob/9ccfe03644f3707f47539b9bec432e6e88516f92/aspnetcore/test/integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/HtmlHelpers.cs)
   can help set that up


Further reading: https://docs.microsoft.com/en-us/aspnet/core/test/integration-tests?view=aspnetcore-2.1 t

### Unit Tests

#### Entity Framework Core In Memory Provider

Entity Framework Core provides a new in-memory provider that can be easier to
use than mocking all your data access behind interfaces. If you are using EF for
simple CRUD commands, this is a great way to test that layer.

To create an instance of your context, pass it some options generated by
`DbContextOptionsBuilder`:

```cs
 var options = new DbContextOptionsBuilder<MyDbContext>()
                .UseInMemoryDatabase(databaseName: "some_name")
                .Options;

var context = new MyDbContext(options);
```

You can set up the data the usual way you would insert/update rows:

```cs
using (Var context = new MyDbContext(options))
{
    context.Customers.Add(new Customer(){ Name = "Bob" });
    context.SaveChanges();
}

var controller = new CustomerController(new MyDbContext(options));

// do something with the controller
```

Even though `CustomerController` got its own brand-new instance of
`MyDbContext`, it will have access to the data since they share the same
`options` parameter. The `"name"` parameter to `UseInMemoryDatatabase` sets the
context for what data will be available: tests that use the same name will have
the same context.

Be careful not to have tests dependent on the order in which they are run. Each
test should probably use its own named context unless you have reason to share
some setup data.

Futher reading: https://docs.microsoft.com/en-us/ef/core/miscellaneous/testing/in-memory

# Starter Files

### Program.cs
```cs Program.cs

public static void Main(string[] args)
{
    CreateWebHostBuilder(args)
        .Build()
        .Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    return WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureAppConfiguration(appConfig => { appConfig.AddUserSecrets<Startup>(); })
        .ConfigureSerilog();
}
```

### SerilogConfigurationExtensions.cs

Complicated setup steps should be extracted to an extension method so that the
main configuration steps are easy to follow

```cs SerilogConfigurationExtensions.cs
public static class SerilogConfigurationExtensions
{
    public static IWebHostBuilder ConfigureSerilog(this IWebHostBuilder builder)
    {
        return builder.UseSerilog((hostingContext, loggerConfig) =>
            {
                var config = loggerConfig
                    .Enrich.FromLogContext()
                    .WriteTo.Console()
                    .WriteTo.RollingFile("logs/log-{Date}.txt", restrictedToMinimumLevel: LogEventLevel.Information)

                    // example: write json-structured events to a file
                    //.WriteTo.RollingFile(new JsonFormatter(renderMessage: true), "logs/log-{Date}.json.txt", restrictedToMinimumLevel: LogEventLevel.Information);
                    ;

                if (hostingContext.HostingEnvironment.IsDevelopment())
                {
                    config.MinimumLevel.Debug()
                        .MinimumLevel.Override("Microsoft", LogEventLevel.Debug);
                }
                else
                {
                    config.MinimumLevel.Information()
                        .MinimumLevel.Override("Microsoft", LogEventLevel.Information);
                }
            });
    }
}
```

### Startup.cs

```cs Startup.cs
public class Startup
{
    public Startup(IConfiguration configuration)
    {
        Configuration = configuration;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        // how to add a db context
        services.AddDbContext<ApplicationDbContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("DbConnection")));

        // you could have more than one context
        services.AddDbContext<AdminDbContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("DbConnection")));

        // how to add ASP.NET Core Identity
        services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();

        // configure identity
        services.Configure<IdentityOptions>(options =>
        {
            // Password settings
            options.Password.RequireDigit = true;
            options.Password.RequiredLength = 8;
            options.Password.RequireNonAlphanumeric = false;
            options.Password.RequireUppercase = true;
            options.Password.RequireLowercase = false;
            options.Password.RequiredUniqueChars = 6;

            // Lockout settings
            options.Lockout.DefaultLockoutTimeSpan = TimeSpan.FromMinutes(30);
            options.Lockout.MaxFailedAccessAttempts = 10;
            options.Lockout.AllowedForNewUsers = true;

            // User settings
            options.User.RequireUniqueEmail = true;
        });

        services.ConfigureApplicationCookie(options =>
        {
            // Cookie settings
            options.Cookie.HttpOnly = true;
            options.ExpireTimeSpan = TimeSpan.FromMinutes(30);
            // If the LoginPath isn't set, ASP.NET Core defaults
            // the path to /Account/Login.
            options.LoginPath = "/auth/login";
            // If the AccessDeniedPath isn't set, ASP.NET Core defaults
            // the path to /Account/AccessDenied.
            options.AccessDeniedPath = "/auth/denied";
            options.SlidingExpiration = true;
        });

        services.AddMvc()
            .AddFeatureFolders();

        // finally, add your application specific services
        services.AddTransient(s => new DapperQueries(Configuration.GetConnectionString("DbConnection")));

         // This is a good place to configure Automapper if you're using it
        AutoMapper.Mapper.Initialize(config =>
        {
            config.AddProfiles(typeof(Startup));
        });

    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ApplicationDbContext dbContext)
    {
        if (env.IsDevelopment())
        {
            app.UseBrowserLink();
            app.UseDeveloperExceptionPage();
        }
        else
        {
            app.UseExceptionHandler("/Home/Error");
        }

        app.UseStaticFiles();

        // add ASP.NET Identity Core to the pipeline
        app.UseAuthentication();

        app.UseMvc(routes =>
        {
            routes.MapRoute(
                name: "default",
                template: "{controller=Home}/{action=Index}/{id?}");
        });
    }
}
```