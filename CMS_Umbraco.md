# Umbraco

**For CMS versions**

The following applies to Umbraco v7.0.0 and above. They likely apply to <v7.0.0 in most cases. Umbraco v6 specific considerations can be found at the bottom of this document.

## Installation and Setup

### Base Umbraco Setup

1. Create a new `ASP.NET Web Application`.  Select a `Empty ASP.NET 4.5.X Template`.  MUST NOT add references for MVC.  The appropriate version will be brought in when the Umbraco NuGet Package is installed.

2. Umbraco installations SHOULD be installed using [Umbraco NuGet Package](https://www.nuget.org/packages/UmbracoCms/).

3. umbracoSettings.config SHOULD have the following modifications:

    1. Update `web.routing` `trySkipIisCustomErrors` to `True`, so IIS 7.5 error messages will be disabled and custom 404 and 500 pages may be used.

4. The following web.config modifications SHOULD be applied:

    1. Update `umbracoDebugMode` in web.config to `true`.  This allows you to perform profiling on the website during development by post-pending `?umbDebug=true` to the URL.

        1. Profiling requires that `@MiniProfiler.RenderIncludes()` is added to your master template.

    2. Update `system.web` `customErrors` mode to `off`, so IIS 7.5 error messages will be disabled and custom 404 and 500 pages may be used.

    3. [Umbraco >7.2.8] Add `<add key="Umbraco.ModelsBuilder.Enable" value="false" />`, as we're using Vault to hydrate strongly-typed models, rather than Umbraco's model builder.

    4. Set 404 node IDs `<add key="pageNotFoundNodeId" value="{{node id}}" />`

    5. Add rewrite rule for IP white list to protect Umbraco directory in web.config:
    ```xml
    <rule name="Restrict Umbraco Access" stopProcessing="true">
        <match url="^umbraco(?!/(api|surface)/)" />
        <conditions>
            <!-- Nerdery -->
            <add input="{REMOTE_ADDR}" pattern="10\.(.*)" negate="true" />
            <add input="{REMOTE_ADDR}" pattern="127\.0\.0\.1" negate="true" />
            <add input="{REMOTE_ADDR}" pattern="204\.62\.150\.2" negate="true" />
            <!-- Client -->
            ...
        </conditions>
        <action type="CustomResponse" statusCode="403" statusReason="Forbidden" statusDescription="Permission Denied" />
    </rule>
    ```

### Front-End Asset Integration

1. SHOULD follow [FE Boilerplate documentation](https://knowledge.nerdery.com/display/FED/Boilerplate) and work with FE lead to determine best process for integrating the FE build into your project. There is no special setup for Umbraco in regard to the FE Integration, and it can be treated as a normal .NET Project.

3. Script and Styles modification via the Umbraco Back-Office SHOULD NOT be supported. You SHOULD NOT map your Styles and CSS output via Umbraco into your FE Build output folder. In all cases we should avoid allowing the client to write their own custom styles and scripts outside of version control and the FE Build process. If we allowed this, it is likely that the client could write styles or scripts via the Umbraco Back-Office that break the functionality of the website, and it would be difficult to troubleshoot this issue outside of version control. 

### Base Package Installation

1. For more robust data types, the following components are RECOMMENDED for use: [Archetype](https://www.nuget.org/packages/Archetype/), [MultiUrlPicker](https://www.nuget.org/packages/RJP.UmbracoMultiUrlPicker/), and [nuPickers](https://www.nuget.org/packages/nuPickers)

2. If using Azure as an external media provider, it is RECOMMENDED to utilize:

    1. Install [UmbracoFileSystemProviders.Azure](https://www.nuget.org/packages/UmbracoFileSystemProviders.Azure/)

    2. Update `FileSystemProviders.config` to use Azure Media Provider

3. Umbraco Vault SHOULD be installed
    1. Create StartupEventHandler to initialize Vault viewmodel namespace and default controller:
        ```c#
	    public class StartupEventHandler : ApplicationEventHandler
        {
            protected override void ApplicationStarting(UmbracoApplicationBase umbracoApplication, ApplicationContext applicationContext)
            {
                Vault.RegisterViewModelNamespace("MyProject.ViewModels", "MyProject");
                DefaultRenderMvcControllerResolver.Current.SetDefaultControllerType(typeof(VaultRenderMvcController));
                ClassConstructor.SetInstanceFactory(new ProxyInstanceFactory());
            }
        }
	    ```
    2. You may enable Vault object cache duration in Web.config `<add key="Vault.ObjectCacheSeconds" value="0" />`, transform appropriately for production). __Note:__ if your cached object contains request-specific properties, then either do not employ Vault caching or refactor these objects.

5. Server Side Image Cropping : Umbraco ships with ImageProcessor .NET library for server side image configuration
    1. Configure `cache.config` and `security.config` appropriately

6. .gitignore MUST be configured with the following additional entries
    ```
    # Ignore unimportant folders generated by Umbraco
    **/App_Data/ClientDependency/
    **/App_Data/ExamineIndexes/
    **/App_Data/Logs/
    **/App_Data/[Pp]review/
    **/App_Data/TEMP/

    # Ignore Umbraco content cache file
    **/App_Data/umbraco.config
    **/App_Data/cache
    **/media/cache

    # Don't ignore Umbraco packages (VisualStudio.gitignore mistakes this for a NuGet packages folder)

    # Make sure to include details from VisualStudio.gitignore BEFORE this
    !**/{{ProjectNamespace}}.Web/App\_Data/packages/*
    !**/{{ProjectNamespace}}.Web/[Uu]mbraco/developer/[Pp]ackages/*

    # Umbraco Ignore Custom
    umbraco.licensing.log.txt #Licensing Logs
    umbraco.licensing.log.txt.orig #Licensing Logs
    courier/cache #Courier Cache [if using Courier]
    courier/revisions #Courier Revision Data [if using Courier]
    ```

7. Swashbuckle SHOULD be installed to provide API documentation for JavaScript developers.
    1. Enable "XML Documentation File" (under Project -> Build)

    2. Update Swashbuckle routing so it doesn't conflict with Umbraco:
        ```c#
        .EnableSwagger("apispec/{apiVersion}", c => ...)
        .EnableSwaggerUi("apidocs/{*assetPath}", c => ...)
        ```

8. Semantic versioning SHOULD be utilized; CroMagVersion MAY be utilized to perform this *via Nerdery's NuGet*: [https://nuget.nerderylabs.com/nuget/](https://nuget.nerderylabs.com/nuget/)
    1. Add extension method to put version in footer:
        ```c#
        public static HtmlString GetVersionInfoHtml(this HtmlHelper<BaseViewModel> h)
        {
    	    var v = FileVersionInfo.GetVersionInfo(Assembly.GetExecutingAssembly().Location);
    	    var sb = new StringBuilder();
    	    #if !(DEBUG)
    		    sb.Append("<!--");
    	    #endif

    	    sb.Append(new HtmlString($"- {v.FileVersion}-{GetAssemblyConfiguration()}"));

    	    \#if !(DEBUG)
    		    sb.Append("-->");
    	    \#endif

    	    return new HtmlString(sb.ToString());
        }
	    ````

9. 301 Redirect Helper MAY be installed if the client needs to manage redirects in the CMS. [Simple 301](https://our.umbraco.org/projects/backoffice-extensions/simple-301/)

10. MAY install [FALM Housekeeping Tools](https://our.umbraco.org/projects/backoffice-extensions/falm-housekeeping/) (to remove development history and logs before client hand off)

### Templates

Relevant External Documentation:

* [URL Hijacking with Custom Controller](https://our.umbraco.org/documentation/reference/routing/custom-controllers)

All templates SHOULD reside in the **~/Views **folder. Each template SHOULD use a strongly typed view model rather than using the Umbraco HTML helper methods. If the template cannot be rendered using only CMS content (i.e. server needs to look something up from database to render as well), this information SHOULD be a property of the view model and hydrated using URL Hijacking with a Custom Controller. HTML Actions SHOULD only be used for the master layout where a specific controller cannot be identified.

```c#
public class HomePageController : RenderMvcController
{
    public override ActionResult Index(RenderModel model)
    {
        var vm = Vault.Context.GetCurrent<HomePageViewModel>();
        vm.DerivedProperty = _service.GetThing();

        return CurrentTemplate(vm);
    }
}
```

## Forms

Relevant External Documentation:

* [MVC Forms](https://our.umbraco.org/documentation/Reference/Templating/Mvc/forms)

All forms SHOULD include an Anti-Forgery Token that is validated by the controller form handler.

**Example Controller:**

```c#
public class MyFormController : RenderMvcController
	[HttpPost]
	[ValidateAntiForgeryToken]
	public ActionResult MyForm(MyFormInfo request)
	{
		if (!ModelState.IsValid)
			return View(request);
		try
		{
		// Do stuff with form inputs
		}
		catch (Exception ex)
		{
			LogHelper.Error<MyFormController>("Error occurred submitting form", ex);
			ModelState.AddModelError("", "There was an error submitting the form.  Please try again at a later time.");
			return View(request);
		}

		TempData["Success"] = true;
		return RedirectToAction("Index");
	}
}
```

## Macros
In general, it is not recommended to use the Umbraco Macros functionality. The WYSIWYG editing experience with macros has historically been a broken experience and difficult to manage.

## Custom Back-Office Dashboards & Property Editors
Relevant External Documentation:

* [Creating a Custom Dashboard](https://our.umbraco.org/documentation/Tutorials/Creating-a-Custom-Dashboard/)
* [Creating a Property Editor](https://our.umbraco.org/documentation/Tutorials/Creating-a-Property-Editor/)

Custom dashboards for the Umbraco Back Office SHOULD be developed using the Umbraco provided package.manifest architecture with a plugin hosted in the `App_Plugins` directory. The standard has moved away from developing these sections entirely in .NET and Razor into something with more parity to how Umbraco manages their own sections with Angular backed by an .NET API Controller.

## Document Types

All document types SHOULD have an appropriate icon associated to show in the content tree. All document properties SHOULD be organized in logical groups in separate tabs. Each property MUST have an appropriate description and if required by the template, and MUST be marked as mandatory.

Document Type Compositions SHOULD be used for document types that utilize the same set of properties. For example, a Document Type should be created for `Meta Data` and all user facing document types should inherit the `Meta Data` document type via Composition. This was historically achieved through hierarchical inheritance in document folder structure, but has not moved towards this composition based approach. 

## Configuration

For app settings and custom configuration sections, follow [.NET standards for configuration](https://git.nerdery.com/projects/BRAVO/repos/dot-net-standards/browse/configuration.md). For the Umbraco configurations in the "/config" folder, MAY use SlowCheetah or similar tool to create transforms for configurations that vary between environments.

## Deployments & Continuous Integration

Relevant External Documentation:

* [Security Best Practices](https://umbraco.com/follow-us/blog-archive/2014/7/21/security-issues-found-in-umbraco-4-6-and-7/)

After installing/upgrading Umbraco, follow the actions outlined in [Security Best Practices](https://umbraco.com/follow-us/blog-archive/2014/7/21/security-issues-found-in-umbraco-4-6-and-7/).

### Production Web Config Transformations

```xml
<add key="umbracoDebugMode" value="false" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
<add key="umbracoUseSSL" value="true" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
```

```xml
<system.web>
    <compilation xdt:Transform="RemoveAttributes(debug)" />
</system.web>
```

### Robots.Txt Transformations

The following Target can be added to your publish profile to transform your robots.txt files:

```xml
<Target Name="DeployRobotsFile">
    <ItemGroup>
        <_CustomFiles Include="robots.$(ConfigurationName).txt" />
        <FilesForPackagingFromProject Include="%(_CustomFiles.Identity)">
            <DestinationRelativePath>robots.txt</DestinationRelativePath>
        </FilesForPackagingFromProject>
    </ItemGroup>
</Target>
```

### Clearing Cache on Deployments

The Umbraco cache MAY be cleared on your destination environment by adding the following target to your publish profile.  This is necessary if a stale server is being "swapped" into production using a load balancer:

```xml
<Target Name="RemoveCacheFiles" AfterTargets="PipelineDeployPhase;MSDeployPublish;Package">
    <Message Text="Deleting Umbraco cache files off server..." Importance="high" />
    <Exec Command="&quot;$(MSDeployPath)\msdeploy.exe&quot; -verb:delete -dest:contentPath=&quot;$(DeployIisAppPath)\App_Data\umbraco.config&quot;,computerName=&quot;$(MSDeployServiceURL)&quot;,authType=Basic,userName=$(UserName),password='$(Password)' -allowUntrusted" />
    <Exec Command="&quot;$(MSDeployPath)\msdeploy.exe&quot; -verb:delete -dest:contentPath=&quot;$(DeployIisAppPath)\App_Data\TEMP\ExamineIndexes&quot;,computerName=&quot;$(MSDeployServiceURL)&quot;,authType=Basic,userName=$(UserName),password='$(Password)' -allowUntrusted" />
</Target>
```

### Finding Build Errors on Views

You SHOULD set up MVCBuildViews for compiling of razor views on release build, so the build server catches any runtime issues in your razor files:

```xml
<PropertyGroup>
    <MvcBuildViews>false</MvcBuildViews>
</PropertyGroup>
<Target Name="MvcBuildViews" AfterTargets="CopyFilesToOutputDirectory" Condition="'$(MvcBuildViews)'=='true'">
    <AspNetCompiler VirtualPath="temp" PhysicalPath="$(WebProjectOutputDir)" ContinueOnError="false" />
</Target>
```

`"C:\Program Files (x86)\MSBuild\14.0\Bin\MSBuild.exe" ..\Your.sln /p:MvcBuildViews=True`

## Internationalization

Umbraco sites that support internationalization SHOULD use a "multiple sites" model where each supported language has it's own website. Each website SHOULD be a root level item in the Umbraco content tree. All copy SHOULD be moved into the specific CMS document types and templates it relates to. If there is common copy that is used on a collection of pages (i.e. text for close modal button), it SHOULD be stored on the root node and accessed recursively.

### Front-End Internationalized Integration

Any templating performed by front-end (such as Handlebars or Mustache templates) SHOULD be provided as an HTML script element in the server side view as opposed to compiled handlebars templates so internationalized content can be provided via the CMS.

Client side validation messages output by plugins such as the unobtrusive jQuery validation plugin SHOULD be localized to the site's language through the use of special model attributes which get the validation message from appropriate page in the CMS.

## Performance

* SHOULD Enable gzip compression

# Additional Libraries and Tools

## ORM Usage

Relevant External Documentation:

* [Umbraco Vault](https://github.com/thenerdery/UmbracoVault)

* [Using Umbraco Peta Poco API's](http://www.wiliam.com.au/wiliam-blog/using-petapoco-with-umbraco-is-pretty-sweet)

The RECOMMENDED ORM for Document, Media, and Data Types is Umbraco Vault. Umbraco Vault is open source, but developed and actively maintained by the Nerdery. It is easily extensible and provides strongly typed viewmodel mapping, automatic model injection into views, and easy querying of Umbraco.

For custom tables in the Umbraco database, the Umbraco Peta Poco API's SHOULD be used. Peta Poco is a lightweight, dependency-free ORM built in to Umbraco. Umbraco supplies a database wrapper which can be obtained through the ApplicationContext.Current object. This [tutorial](http://www.wiliam.com.au/wiliam-blog/using-petapoco-with-umbraco-is-pretty-sweet) is a good set of instructions to get going with Umbraco and Peta Poco.

# Virtual Routes

Virtual routes MAY be utilized as a way to surface content that is not defined in the CMS. Virtual routes are the RECOMMENDED method to surface outside information. Settin up virtual routes is as simple as defining a route in the normal MVC way (via RouteConfig.cs in App_Start), but utilizing an Umbraco provided `MapUmbracoRoute` extension method. If you are surfacing data that is outside of the CMS Content Tree you still need to give Umbraco some context by pointing it to a content item in the CMS that is relevent to the current request.

```C#
public static void RegisterRoutes(RouteCollection routes)
{
    // Umbraco back-end routes
    routes.MapRoute(
	"Default Umbraco Route",
	"umbraco/{controller}/{action}",
	new { area = "Umbraco", action = "Index" }
    );

    // Custom virtual route
    routes.MapUmbracoRoute(
	"Custom Foo Handler Route",
	"/Foo/{id}",
	new { controller = "Foo", action = "FooDetail" },
	new CustomFooRouteHandler()
    );   
}

/// <summary>
/// Simple sub-class that handles setting the Umbraco context
/// based on custom routing. Sets Foo as the umbraco context
/// </summary>
private class ContentFooRouteHandler : UmbracoVirtualNodeRouteHandler
{
    protected override IPublishedContent FindContent(RequestContext requestContext, UmbracoContext umbracoContext)
    {
	var helper = new UmbracoHelper(umbracoContext);
	return helper.GetContentByName("foo").FirstOrDefault();
    }
}

```


## Media

### Media Types

The default "Image" media type SHOULD be modified to include a property editable by the content editor which should hydrate the images “alt” HTML attribute.

### External Blob Storage

Relevant External Documents:

* [Azure Media Storage Plugin (< v6.2.5)](https://github.com/idseefeld/UmbracoAzureBlobStorage)

* [Azure Media Storage Plugin (>= v6.2.5)](https://our.umbraco.org/projects/collaboration/umbracofilesystemprovidersazure/)

* [Amazon AWS E3 Media Storage Plugin](https://bitbucket.org/gibedigital/umbraco-amazons3provider)

If application is being hosted on a cloud environment, media SHOULD be stored in a cloud storage service. Links for storage plugins have been included above. If the plugin allows it, media URL's SHOULD be virtual URL's relative to the application. This will give the appearance the media is hosted by the same web server as the application and will provide seamless integration with query string based image processors.

## Backoffice Users

### Creation

The user setup during the initial Umbraco installation is considered by Umbraco to be a super user. Any custom sections added to the Umbraco dashboard will be inaccessible to all users except for the super user. The superuser can grant access to any user, and after that point, those users can grant access to the custom section as well. In the event the super user is deleted, permissions will need to be granted to custom section via direct SQL queries.


# Umbraco v6 Consideration
For applications that utilize < v7.0.0, there are a few special considerations and configurations:

umbracoSettings.config SHOULD have the following modifications:

    1. [Umbraco <7.0.0] Update `defaultRenderingEngine` in to be `MVC`.
    
The following web.config modifications SHOULD be applied:

    1. [Umbraco <7.0.0] Update `umbracoUseDirectoryUrls` in web.config to `true`.  Directory URLs are typically favored by clients.
    
For more robust data types, the following components are RECOMMENDED for use: [uComponents](https://www.nuget.org/packages/uComponents/) [Umbraco <7.0.0]

[Umbraco <7.0.0] Install [ImageProcessor](https://www.nuget.org/packages/ImageProcessor/) SHOULD be installed for server-side image resizing.  

301 Redirect Helper SHOULD be installed ([Nerdery fork](https://github.com/banderso-n/UrlTracker/) that doesn't bring down production websites; this is important) [Umbraco <7.0.0]

