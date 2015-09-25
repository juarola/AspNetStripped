#AspNetStripped#

Not ready for asp.net 5 yet? Would you still prefer to use standard tools for client-side development?

Here's how to strip out asp.net bundling, minification and nuget-based client-side package management from a vanilla Visual Studio 2015 ASP.NET (MVC template)  4.6 project.

##1. Project initialization##

Add a new asp.net 4.6 (or 4.5.x if you have such tendencies) project in VS2015. Select MVC template.

##2. Remove nugets##

Get rid of nuget-based management of client-side libs and remove bundling/minification -related server side libs

Uninstall the following Nuget Packages - "Uninstall-Package *PACKAGE NAME HERE*" or using the VS's ui.

Client side libs:

- bootstrap
- Microsoft.jQuery.Unobstrusive.Validation
- jQuery.Validation
- jQuery
- Modernizr
- Respond

Optimization libs:

- Microsoft.AspNet.Web.Optimization
- WebGrease
- Antlr



##3. Remove server side code and configuration##
Remove or comment out all code in BundleConfig.cs.

Remove using statement from app's entry point (global.asax.cs):

`using System.Web.Optimization;`

Remove asp.net bundle registration from app's entry point (global.asax.cs):

`BundleConfig.RegisterBundles(BundleTable.Bundles);`

Delete bindings for System.Web.Optimization and WebGrease from Web.config. They look like this:
``` 
<dependentAssembly>
    <assemblyIdentity name="System.Web.Optimization" publicKeyToken="31bf3856ad364e35" />
    <bindingRedirect oldVersion="1.0.0.0-1.1.0.0" newVersion="1.1.0.0" />
</dependentAssembly> 
```

##4. Configure NPM##

Create npm configuration. There are at least two options you can choose from:

###Option 1###

Run NPM init 

*NB to get to command line, try out [Open Command Line extension](http://bit.ly/1L0YHZh]) by Mads Kristenssen. Loads of options, ability to execute different shells etc.*

###Option 2###

right click on project -> "add new item" -> select "NPM config file"-template

##5. Add and configure Bower##

Open package.json and add bower to dev-time dependencies:
```
{
    "version": "1.0.0",
    "name": "ASP.NET",
    "private": true,
    "devDependencies": {
        "bower": "1.4.1"
    }
}
```

**Take notice how the bower package is automatically installed when package.json is saved!**

Ensure .bowerrc is included into the project ("add existing item"). 

Change bower's package path in .bowerrc as required or leave it as it is. Maybe you'd like to use ~/bower_modules which lots of people prefer.

Add new bower.json file to project (use "bower init" in shell or "add new item" and select "Bower Configuration file" item template via vs ui).

##6. Add deps in Bower##

Add bootstrap, jquery-validation-unobtrusive, modernizr and respond to dependencies:
```
{
    "name": "AspNetStripped",
    "private": true,
    "dependencies": {
        "bootstrap": "*",
        "jquery-validation-unobtrusive": "*",
        "modernizr": "*",
        "respond": "*"
    }
}
```

**It takes a few seconds, but notice how these packages and their dependencies are automatically installed into the dir you configured previously when bower.json is saved!**

##7. Fix references to client-side libs##

Make the following change into _layout.cshtml. 

*Remember to check that the paths match the ones Bower loaded the packages into.*

**Bootstrap and site's own css**

Change:

`@Styles.Render("~/Content/css")`

To:

`<link rel="stylesheet" href="~/bower_modules/bootstrap/dist/css/bootstrap.min.css" />`

...and...

`<link rel="stylesheet" href="~/Content/Site.css" />`

**Modernizr**

Change:

`@Scripts.Render("~/bundles/modernizr")`

To:

`<script src="~/bower_modules/modernizr/modernizr.js" ></script>`

**jQuery**

Change:

`@Scripts.Render("~/bundles/jquery")`

To:

`<script src="~/bower_modules/jquery/dist/jquery.min.js"></script>`

**Bootstrap css:**

Change:

`@Scripts.Render("~/bundles/bootstrap")`

To:

`<script src="~/bower_modules/bootstrap/dist/js/bootstrap.min.js"></script>`

..and...

`<script src="~/bower_modules/respond/dest/respond.min.js"></script>`


**DONE (for now)**



**At this point you have effectively switched from using Nuget to using Bower for all client-side runtime libs. You've also prepared the project for running Gulp for all your minification, transpilation, bundling etc. dev-time needs.**



