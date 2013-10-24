---
layout: github.io
category: pages
title: SoftwarePotential.Configuration.Web-README
tagline: README for SoftwarePotential.Configuration.Web NuGet package from Software Potential
---

Welcome to the SoftwarePotential.Configuration.Web-&lt;PermutationShortCode&gt; NuGet Package.

IMPORTANT: this package is intended to be used in concert with:

* SoftwarePotential.Licensing-&lt;MyProduct&gt;-&lt;MyVersion&gt; NuGet package (must be installed for the code to compile correctly). See [the associated Licensing README](http://docs.softwarepotential.com/Licensing-README.html) for more information.
* SoftwarePotential.Protection-&lt;PermutationShortCode&gt; NuGet package. See [the associated Protection README](http://docs.softwarepotential.com/Protection-README.html) for more information.

# Key Constraints and Advantages of this WEB Configuration Package

This package is intended to offer a licensing configuration for ASP.NET applications that manage licenses completely within the current application's App\_Data folder.
As the license store directory needs to be permissioned properly during web application deployment (so that it's accessible by the ASP.NET hosting process - usually IIS worker process), the package can be used in conjunction with [Microsoft Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) tool.

# License Storage Location Base Path

Adding this NuGet package to your application configures licenses to be stored in the App\_Data\\Licenses folder within current web application.

# Deploying to IIS
Web applications that reference this NuGet package are intended to be deployed to an IIS Web Server using WebDeploy. The simplest way to do that is to use Publish Web wizard in Visual Studio.

**NB the application stores licenses under `<application base directory>\App_Data\Licenses`. When (re)deploying, it is critical to ensure that the publish process does not delete any licenses that users may have activated**. To do this:
* In Visual Studio 2010: Ensure the *`Leave extra files on destination (do not delete)`* option (in Publish Web dialog) is **checked**
* In Visual Studio 2012 or 2013: Ensure the *`Remove additional files at destination`* option (in Publish Web dialog) is **_un_checked**
