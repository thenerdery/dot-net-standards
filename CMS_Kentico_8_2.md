Kentico
===========================================
**For CMS version: 8.2**

# Installation and Setup

* SHOULD use the Kentico 8.2 installer
    * MUST use Custom Installation
    * SHOULD use "Prepare for installation on a remote server"
    * MUST verify client’s intended .NET Framework version and select correlating version (.NET Framework 4.0 or .NET Framework 4.5 / 4.5.1)
    * MUST use Web application project
    * MUST use "I have access to SQL server" using a database that matches client’s production SQL Server version, shared amongst team developers
    * SHOULD install all modules
    * SHOULD use "Blank Site ASPX" Sample site
    * MUST rename solution to use appropriate Namespace convention
    * MUST NOT rename CMSApp project or change namespace
    * MUST verify client’s intended production Kentico Hotfix version and apply hotfixes and MAY use KIM (Kentico Installation Manager), alternatively download hotfixes and apply manually

### References
* [https://docs.kentico.com/display/K82/Installation](https://docs.kentico.com/display/K82/Installation)
* [https://docs.kentico.com/display/K82/Kentico+Installation+Manager](https://docs.kentico.com/display/K82/Kentico+Installation+Manager)

## Source Control
* MUST ensure when commit project to source control that deployment mode is OFF
* MUST commit installation to source control using the following lines in the .gitignore
* MAY store virtual objects on the file system for source control collaboration instead of in the database

```
# Private configuration
	ConnectionStrings.config
	AppSettings.config

# Ignore application data
	**/App_Data/*
	# But keep localization and a list of MIME types
!**/App_Data/mimetypes.txt

### Kentico ###

# Internal dependencies
CMSDependencies/

# Language resources
CMSResources/
```
### References* [https://docs.kentico.com/display/K82/Deployment+mode+for+virtual+objects](https://docs.kentico.com/display/K82/Deployment+mode+for+virtual+objects)

## Client-Side Boilerplate Integration
* MUST create a Site using the Kentico Sites application using a meaningful Site Code Name
* SHOULD use a Site Code Name consistent with namespacing strategy
* MUST create a directory in ~/App_Themes directory using Site Code Name and request client-side developers to add client-side asset dispersal to this directory in the Grunt Build
* MUST create a directory in ~/CMSTemplates directory using Site Code Name and create a Master Page Template using ASPX integrating client-side scripts and styles
* SHOULD stub out Page Templates and Web Parts BEFORE client-side efforts begin to understand constraints around HTML generation of stock Kentico Web Parts

### References
* [https://docs.kentico.com/display/K82/Creating+new+sites+using+the+wizard](https://docs.kentico.com/display/K82/Creating+new+sites+using+the+wizard)
* [https://docs.kentico.com/display/K82/Export+folder+structure](https://docs.kentico.com/display/K82/Export+folder+structure)

# Architecture Best Practices

## Page Types and Custom Tables
* SHOULD use stock Kentico Page Types where they meet the requirements or inherit from stock Kentico Page Types to create new Page Types
* MUST NOT modify stock Kentico Page Types
* MUST use Custom Tables where data/content is not directly related to a page
* SHOULD namespace custom Page Types and Custom Tables with "Custom"
* SHOULD apply scoping to page types where page types should only be allowed under a certain page type or other scope
* SHOULD consider Web Parts for modular elements before a Page Type or Custom Table
* SHOULD define appropriate default template for Page Types

### References
* [https://docs.kentico.com/display/K82/Page+types](https://docs.kentico.com/display/K82/Page+types)
* [https://docs.kentico.com/display/K82/Custom+tables](https://docs.kentico.com/display/K82/Custom+tables)
* [https://docs.kentico.com/display/K82/Limiting+the+pages+users+can+create](https://docs.kentico.com/display/K82/Limiting+the+pages+users+can+create)

## Web Parts
* SHOULD use stock Kentico Web Parts where the meet the requirements
* MUST create a directory in ~/CMSWebParts directory using Site Code Name.
    * SHOULD preface name with "Custom" for custom web parts and modules (i.e. if site code name is MySite, directory name is CustomMySite) if creating custom web parts
* SHOULD identify Web Part Containers for client-side tiers and cards to allow clients to add stock web parts with design constraints
* MAY consider editable web part

## Page Templates
* MUST use ASPX Master Page
* MUST use Portal + ASPX Page Templates
* SHOULD use an appropriate inheritance strategy, such as Page Nesting
* SHOULD avoid Ad-Hoc Templates during initial development

### References
* [https://docs.kentico.com/display/K82/Creating+master+pages+for+ASPX+templates](https://docs.kentico.com/display/K82/Creating+master+pages+for+ASPX+templates)
* [https://docs.kentico.com/display/K82/Using+both+ASPX+and+portal+templates+on+a+single+site](https://docs.kentico.com/display/K82/Using+both+ASPX+and+portal+templates+on+a+single+site)
* [https://docs.kentico.com/display/K82/Inheriting+portal+engine+page+content](https://docs.kentico.com/display/K82/Inheriting+portal+engine+page+content)

## Pages & Navigation
* SHOULD use CSS List Menu Web Part for menus, but MUST stub out menu BEFORE client-side efforts begin due to significant requirements

### References
* [https://docs.kentico.com/display/K82/CMSListMenu](https://docs.kentico.com/display/K82/CMSListMenu)

## Configuration
* MUST create a custom Module and add settings to the Kentico "Settings" Application

### References
* [https://docs.kentico.com/display/K82/Adding+custom+website+settings](https://docs.kentico.com/display/K82/Adding+custom+website+settings)

## Deployment
* MUST install Kentico on target server (see also, Installation and Setup)
* SHOULD Export Site using Kentico "Sites" Application
* SHOULD ensure all objects are stored on the file system rather than database using the Kentico "System" Application prior to deployment

### References
* [https://docs.kentico.com/display/K82/Exporting+sites](https://docs.kentico.com/display/K82/Exporting+sites)
* [https://docs.kentico.com/display/K8/Deployment+mode+for+virtual+objects](https://docs.kentico.com/display/K8/Deployment+mode+for+virtual+objects)

## Internationalization
* MUST use Kentico Culture Support

### References
* [https://docs.kentico.com/display/K82/Setting+up+multilingual+websites](https://docs.kentico.com/display/K82/Setting+up+multilingual+websites)’

## Security
* MUST use Kentico Security Checklist while Designing, Developing, and Deploying a website

### References
* [https://docs.kentico.com/display/K82/Securing+websites](https://docs.kentico.com/display/K82/Securing+websites)

# Additional Libraries and Tools

## Visual Studio

### Project Integration
* SHOULD use Kentico Integration Bus for integration with external systems

#### References
* [https://docs.kentico.com/display/K82/Using+the+integration+bus](https://docs.kentico.com/display/K82/Using+the+integration+bus)

### Configuration
* MUST use Kentico Settings for configuration whenever possible (see also, Configuration)

### Exceptions
* MUST log exceptions to Kentico Event Log

#### References
* [https://docs.kentico.com/display/K82/Working+with+the+system+event+log#Workingwiththesystemeventlog-EventlogAPI](https://docs.kentico.com/display/K82/Working+with+the+system+event+log#Workingwiththesystemeventlog-EventlogAPI)

## ORM Usage
* SHOULD use CMS.DataEngine
* MAY use Entity Framework if performing READ-ONLY queries with Custom Tables
    * MUST NOT use Entity Framework for modifying data in Custom Tables

### References
* [https://docs.kentico.com/display/K82/Working+with+database+queries+in+the+API](https://docs.kentico.com/display/K82/Working+with+database+queries+in+the+API)

# Routes

## Virtual Routes
* SHOULD only customize routes and URL rewriting using Kentico Settings application and Pages application

### References
* [https://docs.kentico.com/display/K82/Configuring+page+URLs](https://docs.kentico.com/display/K82/Configuring+page+URLs)

# Media

## CDN Integration
* MUST use Kentico File System Providers

### References

* [https://docs.kentico.com/display/K81/Configuring+file+system+providers](https://docs.kentico.com/display/K81/Configuring+file+system+providers)

# Users

## Creation

* SHOULD use Kentico Registration or Kentico Users Application

### References
* [https://docs.kentico.com/display/K8/User+management](https://docs.kentico.com/display/K8/User+management)
* [https://docs.kentico.com/display/K8/User+registration+and+authentication](https://docs.kentico.com/display/K8/User+registration+and+authentication)

## Permissions
* SHOULD define permissions using the Kentico Permissions application
* MAY consider e-Commerce Membership for purchased access
* MAY consider EMS Personas for marketing personalization

### References
* [https://docs.kentico.com/display/K82/Configuring+permissions](https://docs.kentico.com/display/K82/Configuring+permissions)
* [https://docs.kentico.com/display/K82/Membership+management](https://docs.kentico.com/display/K82/Membership+management)
* [https://docs.kentico.com/display/K82/Personas](https://docs.kentico.com/display/K82/Personas)
