*Editors Note: This is adapted from some internal documentation our client side
teams put together with the release of their boilerplate generator a few years ago.
Examples have been adapted to be .NET specific. Some technology choices by the
Front End team have changed in recent years. While some of the patterns described
here may still apply, you will want to work with your Front End lead developer on
building a smooth integration with the new tooling choices.*

# Front-End Boilerplate integration for Back-End Developers

Congratulations! You are the lead developer for a project with back-end
technologies (PHP/.NET/etc). Very likely your Front-End lead will be developing
with the [Nerdery Front-End Boilerplate](https://knowledge.nerdery.com/display/FED/Boilerplate).

The Front-End boilerplate combines a set of tools to create a completely
static, runnable, stand-alone web application. But what if you need to integrate
it into your back-end framework?

## First Steps

### Read the Documentation

The first thing you SHOULD do is familiarize yourself with the Front-End
boilerplate [documentation](https://knowledge.nerdery.com/display/FED/Boilerplate).
It provides a great overview of the functionality and usage of the framework.

### Install Node.Js

Next, all the back-end developers on the project SHOULD install node.js locally,
or you can consider bundling a stand-alone version in your repository.

Why do you need Node.js? Node.js is a server-side JavaScript platform that
ultimately runs Grunt, a command-line task runner that we are using to build
Front-End projects. These tasks include things like turning SASS into CSS,
minifying and bundling JavaScript, etc.

> “But I can minify JavaScript and compile SASS using .NET frameworks and
> tools!” — Me, a month ago

If you find yourself saying the above statement, please see the section below
titled “Appendix A. Why not use back-end minification/bundling tools?”

### Determine Static Website Placement

Third, determine where the Front-End boilerplate should live in your code base.
The typical setup is as follows:

#### .NET MVC

```
\MySolution
   \MyProject.Web
      \_static
```

This makes the static website available at:
http://your.staging.domain/_static/web/index.html

## The Build Process

### Configuring

The high-level settings for the build process are configured in
`_static/build-env.js` (You’ll need to create this from
`build-en.js-dist`).

Very likely, especially when using a CMS, there is a specific place where assets
need to live. The initial thought would be to change the path of `DIR_DEST`,
but we don’t want to do that for the following reasons:

1. We still want a separate static output of the website for quick Front-End
   debugging, sharing with the client, etc.
2. The build process would clear the files in the parent directory of
   `assets`, which will likely remove CMS/Framework files we don’t want
   deleted.
3. The build process copies over the static .html files, which typically don’t
   need to be copied over to the back-end framework, since HTML code typically
   has to be manually integrated with your back-end’s templating system.

So, we’ll start by adding a new build environment variable to `build-env.js`.
Change the relative path to where assets live in your particular framework.

```js
/**
 * Path to the back-end public destination of the assets.  No trailing slash.
 *
 * @property DIR_DEST_PUBLIC
 * @type String
 */
DIR_DEST_PUBLIC: '../assets',
```

Additionally, since we’re referencing a path outside our static directory, we also have to set:

```js
UNSAFE_MODE: true
```

Then we’re going to make a couple changes to the `Gruntfile.js` file.

First, we want to clear out the public assets directory on a build. Add the following line:

```js
grunt.initConfig({
     ...
     clean: {
         ...
         public: ['<%= env.DIR_DEST_PUBLIC %>'],
         ...
     },
     ...
});
```

Next, we want to copy out the assets to the public assets directory:

```js
grunt.initConfig({
     ...
     copy: {
         ...
         // This should go last
         public: {
             files: [{
                 expand: true,
                 cwd: '<%= env.DIR_DEST %>/assets',
                 dest: '<%= env.DIR_DEST_PUBLIC %>',
                 src: '**'
             }]
         }
         ...
     },
     ...
});
```

Finally, we need to add these two new tasks to our build process. Modify the
build registerTask on the bottom of the file:

```js
grunt.registerTask('build', ['clean:dest', 'clean:public', 'media', 'server', 'markup', 'styles', 'scripts', 'copy:public', 'clean:tmp']);
```

### Building

The Front-End boilerplate is actually ‘built’ using `build.sh` (Linux/mac)
and `build.cmd` (windows) in the _static folder.

### Integrating

It would be beneficial to integrate the above build scripts with your current
platform’s build process.

#### .NET MVC

You can add the following to your MyProject.Web’s post-build event:

```
cd $(ProjectDir)_static

if "$(ConfigurationName)" == "Debug" (
   echo "Running Front-End debug build"
   build.cmd
) else (
   echo "Running Front-End release build"
   build.cmd --prod
)
```

## Passing Server-Side Values to JavaScript

Now that we have the Front-End assets (CSS, JS, Images, etc) being built and
properly sent to the correct directory in the back-end framework, one of the
remaining integration pain-points is how to integrate JavaScript in with the
back-end. This includes a couple things:

1. Passing database-driven strings, or server-side generated URLs like
   `Url.Action('Action', 'Controller)`.
2. Loading view-specific modules on specific views.

The below examples use Require.JS.

### Technique A – Data Attributes

One potential solution is to load a view-specific module and pass server-side
data to it by adding HTML5 data attributes to the main div surrounding your
view. For example:

*Views/ControllerName/MyView.cshtml*

Add data attributes to each of your views that have dynamic JavaScript functionality:

```html MyView.cshtml
<div class="mainContent" data-controller="MyView" data-uploadurl="@Url.Action("Load")">
...
</div>
```

*_static/src/assets/scripts/App.js*

Add the following ‘auto-load’ functionality to the existing App.js file:

```js App.js
/**
 * Initializes the application and kicks off loading of prerequisites.
 *
 * @method init
 * @private
 */
proto.init = function() {
    this.initViewControllers();
};

/**
 * Initializes any JavaScript view controllers found.
 *
 * @method initViewControllers
 * @private
 */
proto.initViewControllers = function () {
    var self = this;
    $(document).ready(function () {
        self.initController = self.initController.bind(self);
        $(document).find('[data-controller]').each(self.initController);
    });
};

/**
 * Initializes a specific JavaScript view controller.
 *
 * @method initController
 * @param {Integer} index jQuery passes the index to its forEach loop; unused
 * @param {DOMElement} element The DOM Element to store a reference to
 * @private
 */
proto.initController = function(index, element) {
    var controllerName = $(element).data('controller');
    require(['modules/views/' + controllerName + 'Controller'], function (controller) {
        var controllerInstance = new controller();
        controllerInstance.init($(element));
    });
};
```

*_static/src/assets/scripts/modules/views/MyViewController.js*

Your JavaScript module for your view might look something like this:

```js MyViewController.js
define(['jquery'], function ($) {
    "use strict";

    var MyView = function() {

    };

    MyView.prototype.init = function ($element) {

        this.uploadUrl = $element.data('uploadurl');

        ...
    };

    ...

    return MyView;
});
```

### Technique B – Inline View JavaScript

Depending on the level of control you have with the templates, it might be
easier to do something like this:

Initialize your view’s module inside a section of each view’s template:

Note: There is no ```<script>``` tag.

```html
@section ExtraScripts {
        require(['jquery', 'views/myView'], function($, myView) {
        $(document).ready(function() {
            var viewController = new myView();
            viewController.init(@Model.SomeVariable);
        });
    });
}
```

Include the extra scripts in the main layout.

Note: The page-specific JavaScript MUST be executed after the main module loads,
so when the JavaScript is bundled, RequireJS won’t attempt go to back out to the
file system to find a file that doesn’t exist.

```html
<script src="assets/vendor/requirejs/require.js"></script>
<script src="assets/scripts/config.js"></script>
<script>
    window.SETTINGS = {}

    ...

    require(['main'], function() {
        /** Include the page-specific JavaScript **/
        @RenderSection("ExtraScripts", required: false)
    });
</script>
```

### Technique A and B – Note about Bundling

When you run the build script with the ‘prod’ flag and the JavaScript is
bundled, the process attempts to be smart about what files are bundled by
starting with main.js and following the dependencies. It does not simply bundle
all the JavaScript it finds in the file system. For both of the above
techniques, we need to manually maintain the list of JavaScript view controllers
that need to be bundled, in the `Gruntfile.js` file.

```js
grunt.initConfig({
    ...
    requirejs: {
        options: {
            ...
        },
        main: {
            options: {
                ...
                // Files we want to explicitly include in the bundle
                include: [
                    'views/MyViewController',
                    'views/MyOtherViewController'
                ]
            }
        }
    },
    ...
})
```

## Appendix A: Why not use back-end minification/bundling tools?

Let’s consider for a moment how modules are defined in RequireJS, a common
framework JavaScript developers use to keep code modular and manage
dependencies:

*module1.js*
```js
define(['jquery'], function($) {
  // Module 1 Code
});
```

*module2.js*
```js
define(['jquery'], function($) {
  // Module 2 Code
});
```

When I require a module, `var myModule = require("module1");`, RequireJS uses
the file name to determine what module to load.

If I use a .NET bundler such as SquishIt or Combres, it will generate this:

```js
define(['jquery'], function($) {
  // Module 1 Code
});

define(['jquery'], function($) {
  // Module 2 Code
});
```

Now RequireJS has no way to figure out which module is which.

But the bundling process built into the Front-End boilerplate will transform
the modules to explicitly name them before combining them:

```js
define("module1", ['jquery'], function($) {
  // Module 1 Code
});

define("module2", ['jquery'], function($) {
  // Module 2 Code
});
```

## Appendix B: Installing 3rd party JavaScript libraries

```sh
bower install PACKAGE_NAME --save # downloads package, updates bower.json
grunt install # updates the requirejs config.js file
```

You SHOULD NOT be manually downloading libraries and manually configuring
require.js.

You can search packages at: http://bower.io/search/