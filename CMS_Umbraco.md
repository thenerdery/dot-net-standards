# Umbraco

**For CMS versions**

Applies for all versions, but some content may differ when specified for:

* **>= 6.0.0 < 7.0.0**

* **>= 7.0.0**

## Installation and Setup

### Base Umbraco Setup

1. Create a new `ASP.NET Web Application`.  Select a `Empty ASP.NET 4.5.X Template`.  MUST NOT add references for MVC.  The appropriate version will be brought in when the Umbraco NuGet Package is installed.

2. Umbraco installations SHOULD be installed using [Umbraco NuGet Package](https://www.nuget.org/packages/UmbracoCms/).

3. umbracoSettings.config SHOULD have the following modifications:

    1. [Umbraco <7.0.0] Update `defaultRenderingEngine` in to be `MVC`.

    2. Update `web.routing` `trySkipIisCustomErrors` to `True`, so IIS 7.5 error messages will be disabled and custom 404 and 500 pages may be used.

4. The following web.config modifications SHOULD be applied:

    1. [Umbraco <7.0.0] Update `umbracoUseDirectoryUrls` in web.config to `true`.  Directory URLs are typically favored by clients.

    2. Update `umbracoDebugMode` in web.config to `true`.  This allows you to perform profiling on the website during development by post-pending `?umbDebug=true` to the URL.

        1. Profiling requires that `@MiniProfiler.RenderIncludes()` is added to your master template.

    3. Update `system.web` `customErrors` mode to `off`, so IIS 7.5 error messages will be disabled and custom 404 and 500 pages may be used.

    4. [Umbraco >7.2.8] Add `<add key="Umbraco.ModelsBuilder.Enable" value="false" />`, as we're using Vault to hydrate strongly-typed models, rather than Umbraco's currently half-baked model builder. 

    5. Set 404 node IDs `<add key="pageNotFoundNodeId" value="{{node id}}" />`

    6. Add rewrite rule for IP whitelist to protect umbraco directory in web.config:
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

### Front-End Boilerplate Integration

1. SHOULD follow [.NET standards for client side integration.](https://git.nerdery.com/projects/BRAVO/repos/dot-net-standards/browse/Client_Side_Boilerplate.md)
2. MIME type for .svg and .hbs in web.config SHOULD be added, to add support for SVG images and Handlebars templates, typically used by our front-end development teams.
    ```xml
    <remove fileExtension=".hbs" />
    <mimeMap fileExtension=".hbs" mimeType="text/plain" />
    <remove fileExtension=".svg" />
    <mimeMap fileExtension=".svg" mimeType="image/svg+xml" />
    ```
3. SHOULD update `umbracoCssDirectory` and `umbracoScriptPath` in web.config to match output location for front-end boilerplate:
    ```xml
    <add key="umbracoCssDirectory" value="~/assets/styles" />
    <add key="umbracoScriptsPath" value="~/assets/scripts" />
    ```
4. SHOULD add front-end boilerplate to pre-build events:
    ```
    cd $(ProjectDir)..\_static
    
    if "$(ConfigurationName)" == "Debug" (
    	echo "Running front-end debug build"
    	build.cmd
    ) else (
    	echo "Running front-end release build"
    	build.cmd --prod
    )
    ```

### Base Package Installation

1. For more robust data types, the following components are RECOMMENDED for use: [uComponents](https://www.nuget.org/packages/uComponents/) [Umbraco <7.0.0] or [Archetype](https://www.nuget.org/packages/Archetype/), [MultiUrlPicker](https://www.nuget.org/packages/RJP.UmbracoMultiUrlPicker/), and [nuPickers](https://www.nuget.org/packages/nuPickers) [Umbraco >=7.0.0] 

2. If using Azure as an external media provider, it is RECOMMENDED to utilize:

    1. Install [UmbracoFileSystemProviders.Azure](https://www.nuget.org/packages/UmbracoFileSystemProviders.Azure/)

    2. Update `FileSystemProviders.config` to use Azure Media Provider

3. For additional icons, users MAY add icons from the [Silk Icon Set](http://www.famfamfam.com/lab/icons/silk/) [Umbraco <7.0.0] or [Belle Icon Pack](https://our.umbraco.org/projects/backoffice-extensions/belle-icon-pack/) [Umbraco >=7.0.0] to `\umbraco\Images\Umbraco` for a better selection of icons for custom document types.

4. Umbraco Vault SHOULD be installed
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

5. [Umbraco <7.0.0] Install [ImageProcessor](https://www.nuget.org/packages/ImageProcessor/) SHOULD be installed for server-side image resizing.  Umbraco 7 comes with ImageProcessor built in.
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

8. Semantic versioning SHOULD be utilized; CroMagVersion MAY be utilized to perform this *via Nerdery's Nuget*: [https://nuget.nerderylabs.com/nuget/](https://nuget.nerderylabs.com/nuget/)
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

9. 301 Redirect Helper SHOULD be installed ([Nerdery fork](https://github.com/banderso-n/UrlTracker/) that doesn't bring down production websites; this is important) [Umbraco <7.0.0] or [Simple 301](https://our.umbraco.org/projects/backoffice-extensions/simple-301/) [Umbraco >=7.0.0] 

10. MAY install [FALM Housekeeping Tools](https://our.umbraco.org/projects/backoffice-extensions/falm-housekeeping/) (to remove development history and logs before client handoff) 

## Architecture Best Practices

1. Base Controllers SHOULD be created
    1. Create base Api Controller (from UmbracoApiController)
    2. Create base Form Controller (from SurfaceController)
    3. Create base Template controller (from RenderMVCController)
2. A Base Document Type with core Umbraco properties SHOULD be created:  umbracoNaviHide, umbracoUrlName, SEO fields (name, description, etc)

### Templates

Relevant External Documentation:

* [URL Hijacking with Custom Controller](https://our.umbraco.org/documentation/reference/routing/custom-controllers)

All templates SHOULD reside in the **~/Views **folder. Each template SHOULD use a strongly typed view model rather than using the Umbraco HTML helper methods. If the template cannot be rendered using only CMS content (i.e. server needs to look something up from database to render as well), this information SHOULD be a property of the view model and hydrated using URL Hijacking with a Custom Controller. HTML Actions SHOULD only be used for the master layout where a specific controller cannot be identified. 

```c#
public class HomePageController : BaseController //inherits from RenderMVCController
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

All forms MUST use the HTML Helper extension method *BeginUmbracoForm*. This method injects some extra hidden fields into the form detected by Umbraco to route to the correct surface controller action along with some Umbraco context. All forms SHOULD include an Anti-Forgery Token that is validated by the surface controller form handler.

**Example Template:**

```c#
@using (Html.BeginUmbracoForm("MyForm", "MyForm", new { }, new { }, FormMethod.Post))
{
    @Html.AntiForgeryToken()
    @Html.LabelFor(...), etc
}
```

**Example Controller:**

```c#
public class MyFormController : BaseFormController //inherits from SurfaceController
{
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult MyForm(MyFormInfo request)
    {
        if (ModelState.IsValid)
        {
            try
            {
                // Do stuff with form inputs
            }
            catch (Exception ex)
            {
                LogHelper.Error<MyFormController>("Error occurred submitting form", ex);
                ModelState.AddModelError("", "There was an error submitting the form.  Please try again at a later time.");
                return CurrentUmbracoPage();
            }

            TempData["Success"] = true;
            return RedirectToCurrentUmbracoUrl();
        }

        return CurrentUmbracoPage();
    }
}
```

## Macros

All macros SHOULD treat each of its parameters as optional since macro parameters cannot be made mandatory. If the output of a macro does not make sense without a parameter, output should be empty, otherwise, handle null parameters as best as possible.

## Editors / Custom Controls

**Relevant External Documentation:**

* [Custom Data Editor using IUsercontrolDataEditor (simpler way)](http://www.nibble.be/?p=24)
* [Custom Data Editor using BaseDataType and IDataType (more complex way)](http://www.nibble.be/?p=50)

All custom data types SHOULD be stored in the database as JSON. Each data type should have it's own custom strongly typed model that the value can be deserialized into. Data type models SHOULD be custom classes and SHOULD NOT be primitive types or built in .NET objects such as *List&lt;T>* to allow for easily extending the model in the future without breaking dependencies. Where possible, settings that control the data type editor's behavior (i.e. which column of database table to pull values from) should be provided through an Umbraco Prevalue Editor, rather than a configuration file or hard-coded. Most custom controls can be accomplished using the simpler way listed above and shown in the example below. The more complex way should only be used in special cases where the simple way cannot be used, for example, an Umbraco built-in data type needs to be reused inside your custom control.

**Example Data Model**

```c#
public class CustomDataModel
{
	public string Property1 { get; set; }
	public string Property2 { get; set; }
}
```

**Example Data Editor**

```c#
public partial class CustomDataEditor : UserControl, IUsercontrolDataEditor
{
	//This is custom data type prevalue setting, such as a database table to use 
	[DataEditorSetting("Custom Setting", isRequired = true, description = "Desc.")]

	public string CustomSetting { get; set; }

	private CustomDataModel _value;

	//Gets/sets value stored in Umbraco
	public object value
	{
		get { return JsonConvert.SerializeObject(_value); }
		set { _value = JsonConvert.DeserializeObject<CustomDataModel>(value); }
	}
}
```

**Example Vault Integration with Doc Type Model**

```c#
[UmbracoEntity(AutoMap = true)]
public class DocTypeViewModel
{
	[UmbracoJsonProperty(typeof(CustomDataModel))]
	public CustomDataModel SpecialProperty { get; set; }
}
```

## Document Types

All document types SHOULD have an appropriate icon associated to show in the content tree. All document properties SHOULD be organized in logical groups in separate tabs. Each property MUST have an appropriate description and if required by the template, and MUST be marked as mandatory.

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

### Microsoft Azure Notes

1. MUST set `/umbraco/Xslt/web.config` build action to "None" to address Web Deploy issues

### External Build Servers

1. Install visual studio build targets nuget, and update csproj file to use it

### Finding Build Errors on Views

You SHOULD set up MVCBuildViews for compiling of razor views on release build, so the build server caches any runtime issues in your razor files:

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

Virtual routes MAY be utilized as a way to surface content that is not defined in the CMS. Virtual routes are the RECOMMENDED method to surface outside information.

Umbraco allows virtual routes to be handled for items that are not defined in the CMS, for example, products that are supplied via a database table. To do this, create a document type and template for the type of page that is virtual, in this case a "Product Detail" document type/template. Then create a single item in the content tree of that type named something like “Product Detail Template”. This page will serve as a template for any content that should be defined for all pages through the CMS and a published content item for Umbraco to render. You will need to create an Umbraco Content Finder. This content finder will be used by Umbraco if no content is found using the default Umbraco routing and right before the 404 handlers. The content finder should validate that the URL is valid, and if so, find your content template and assign it to the request. The handler must then be registered at app start. After the virtual content finder assigns the virtual content, the page is rendered like a usual Umbraco page, so the render controller can assign the product specific properties based off the URL such as SKU, price, etc… All virtual routes should be handled this way as it makes use of Umbraco's content pipeline. 

**Example Virtual Content Finder**

```c#
public class ProductVirtualContentFinder : IContentFinder
{
	public bool TryFindContent(PublishedContentRequest contentRequest)
	{	
		if (ValidateUrl(contentRequest.Uri))
		{
			var publishedContent = FindContent(contentRequest.Uri);
			contentRequest.PublishedContent = publishedContent;
			
			//OPTIONAL: set content request template
			contentRequest.TrySetTemplate("ProductDetail");
		}

		return publishedContent != null;
	}

	private bool ValidateUrl(Uri uri)
	{
		//check database to see if url is valid product url
	}

	private IPublishedContent FindContent(Uri uri)
	{
		//find "Product Detail Template" content from Umbraco
	}
}
```

**Example Virtual Content Finder Registration (Global.asax or Application Startup Handler)**

```c#
public class StartupEventHandler : ApplicationEventHandler
{
	protected override void ApplicationStarting(UmbracoApplicationBase umbracoApplication, ApplicationContext applicationContext)
	{
		ContentFinderResolver.Current.InsertTypeBefore<ContentFinderByNotFoundHandlers, ProductVirtualContentFinder>();
	}
}
```

**Example Render Controller**

```c#
public class ProductDetailController : BaseController (inherits from RenderMVCController)
{
	public override ActionResult Index(RenderModel model)
	{
		var vm = Vault.Context.GetCurrent<ProductDetailViewModel>();
    	vm.Product = _service.GetProduct(Request.Uri);
    	return CurrentTemplate(vm);
	}
}
```

## Media

### Media Types

The default "Image" media type SHOULD be modified to include a “Description” property editable by the content editor which should hydrate the images “alt” HTML attribute.

### External Blob Storage

Relevant External Documents:

* [Azure Media Storage Plugin (< v6.2.5)](https://github.com/idseefeld/UmbracoAzureBlobStorage)

* [Azure Media Storage Plugin (>= v6.2.5)](https://our.umbraco.org/projects/collaboration/umbracofilesystemprovidersazure/) 

* [Amazon AWS E3 Media Storage Plugin](https://bitbucket.org/gibedigital/umbraco-amazons3provider)

If application is being hosted on a cloud environment, media SHOULD be stored in a cloud storage service. Links for storage plugins have been included above. If the plugin allows it, media URL's SHOULD be virtual URL's relative to the application. This will give the appearance the media is hosted by the same web server as the application and will provide seamless integration with query string based image processors.

## Backoffice Users

### Creation

The user setup during the initial Umbraco installation is considered by Umbraco to be a super user. Any custom sections added to the Umbraco dashboard will be inaccessible to all users except for the super user. The superuser can grant access to any user, and after that point, those users can grant access to the custom section as well. In the event the super user is deleted, permissions will need to be granted to custom section via direct SQL queries. 

# Custom Backoffice Sections

Custom Umbraco backoffice sections SHOULD be defined in an MVC Area. The area SHOULD be routed under */umbraco/*. Each custom section SHOULD have it's own Umbraco application and tree, but multiple sections can reside in the same area. 

In order to add the section to the Umbraco back office dashboard, the following config files must be updated:

1. `applications.config`
2. `trees.config`

All controllers used for Umbraco back office sections must inherit from UmbracoAuthorizedController so that access is only granted to logged in back office users.

**Example Section Tree**

```c#
[Tree("Custom Section", "customApp", "Custom Section")]
public class ImportersTree : BaseTree
{
	public ImportersTree(string application) : base(application)
	{ }

	public override void RenderJS(ref StringBuilder javascript)
	{
		javascript.Append(@"function openPage(url) {UmbClientMgr.contentFrame(url); }");
	}

   public override void Render(ref XmlTree tree)
   {
		var nodeTreeList = new List<XmlTreeNode>();
		var firstNode = CreateNode("1", "Node 1", "Node 1", "/umbraco/CustomBackOffice/controller/index", ".sprTreeDoc", ".sprTreeFolder_o");

    	nodeTreeList.Add(firstNode);
    	foreach (var node in nodeTreeList)
    	{
			var currentNode = node;
        	OnBeforeNodeRender(ref tree, ref currentNode, EventArgs.Empty);
        	if (node != null)
        	{
        		tree.Add(node);
        		OnAfterNodeRender(ref tree, ref currentNode, EventArgs.Empty);
        	}
		}
    }

    protected override void CreateRootNode(ref XmlTreeNode rootNode)
    {
        rootNode.NodeType = "root";
        rootNode.NodeID = "0";
        rootNode.Action = $"javascript:openPage('{"/umbraco/CustomBackOffice/controller/index"}');";
        rootNode.Menu = new List<IAction> { ActionRefresh.Instance };
    }

    private XmlTreeNode CreateNode(string nodeId, string nodeType, string text, string action, string icon, string openIcon)
    {
        var node = XmlTreeNode.Create(this);
        node.NodeID = nodeId;
        node.NodeType = nodeType;
        node.Text = text;
        node.Action = $"javascript:openPage('{action}');";
        node.Icon = icon;
        node.OpenIcon = openIcon;
        node.HasChildren = false;
        node.Menu = new List<IAction>();

        return node;
    }
}
```

**Example Backoffice Routing**

```c#
public class UmbracoAreaRegistration : AreaRegistration
{
	public override string AreaName
   	{
		get { return "UmbracoBackOffice"; }
	}

   public override void RegisterArea(AreaRegistrationContext context)
   {
		context.MapRoute(
			"Umbraco_custom_backoffice",
			"Umbraco/CustomBackOffice/{controller}/{action}/{id}",
			new { area = "UmbracoBackOffice", action = "Index", id = UrlParameter.Optional });
   }	
}
```