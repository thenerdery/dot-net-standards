# Episerver

**For CMS version: 11.1**

# Installation and Setup

* MUST use the Episerver Visual Studio extension
    * SHOULD install extension from Visual Studio Gallery (Tools -> Extensions and Updates)
    * MUST create a new project using "Episerver Web Site" project template
    * SHOULD use "Empty" template

### References

* [https://world.episerver.com/documentation/developer-guides/CMS/getting-started/set-up-a-development-environment/](https://world.episerver.com/documentation/developer-guides/CMS/getting-started/set-up-a-development-environment/)
* [https://world.episerver.com/documentation/developer-guides/CMS/getting-started/creating-a-starter-project/](https://world.episerver.com/documentation/developer-guides/CMS/getting-started/creating-a-starter-project/)

## Source Control
* MUST commit installation to source control using the following lines in the .gitignore

### Additional .gitignore Entries
```
# Ignore application data
	**/App_Data/*

### Episerver ###
	*License.config
	modulesbin
```

## Client-Side Boilerplate Integration

* SHOULD follow [.NET standards for client side integration.](https://git.nerdery.com/projects/BRAVO/repos/dot-net-standards/browse/Client_Side_Boilerplate.md)
SHOULD add front-end boilerplate to pre-build events:
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
