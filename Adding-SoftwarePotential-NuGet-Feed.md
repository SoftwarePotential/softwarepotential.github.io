---
layout: github.io
category: pages
title: Getting Started - Adding your Software Potential NuGet Feed
tagline: Setting-up your Software Potential NuGet feed
---

# Software Potential NuGet Packages
Whenever you create or update a Permutation or Product definition in your Software Potential service account a set of NuGet packages is generated in the Software Potential NuGet feed. You can use the NuGet Package Manager in Visual Studio to install the packages required to protect and/or license your .NET application.

See the following Getting Started guides for more details on how to protect and license your application using Software Potential:
http://docs.softwarepotential.com/Getting-Started-With-Code-Protection.html
http://docs.softwarepotential.com/Licensing-With-Licensing.html

The purpose of this document is to guide you through the steps in setting up your Software Potential NuGet package source so that you can access the required packages to protect and license your .NET application. *[It is not intended as a walkthrough on the use of NuGet Package Manager; the following articles provide more details on its use.
http://docs.nuget.org/docs/start-here/managing-nuget-packages-using-the-dialog]*

# Installing NuGet Package Manager in Visual Studio
NuGet Package Manager ships with Visual Studio 2012 forward.  For installation on other versions of Visual Studio please see
http://docs.nuget.org/docs/start-here/installing-nuget

# Adding Software Potential NuGet Source
You can download your Software Potential packages from the following NuGet feed: https://srv.softwarepotential.com/NuGet/nuget **(Please note this URL is case sensitive!)**. 

To add this feed to the NuGet Package Manger in Visual Studio:

 1. Launch NuGet Package Manager (by right clicking the Solution in Solution Explorer in Visual Studio and selecting the **Manage NuGet Packages** menu option)

 2. In the Package Manager UI, select **Setting** -> **Package Manager** -> **Package Sources** and click the **+** button to add a new feed

 3. Set the URL to https://srv.softwarepotential.com/NuGet/nuget, name the feed as **Software Potential**, and then click **OK**

The new Software Potential feed should now be listed as one of the **Online** feeds displayed on the main Package Manager screen.

To access your packages you will need to authenticate to the feed using your Software Potential account credentials (username/password). *[These credentials are cached by Visual Studio and so need only be entered once per Visual Studio session.]*


# Accessing Your NuGet Packages
When you authenticate to the Software Potential source you can access your NuGet packages for installation in your projects. 

The NuGet feed can serve multiple versions of a given NuGet Permutation package and multiple revisions of each version. 

## Permutation Package Identification

Each Software Potential NuGet package will be identified by a combination of a * **Package Id** and a * **Package Version**. The composition of the Package Id and Package Version depends on the nature of the NuGet package i.e. on whether it is related to a Permutation or Product definition.  

For example, 
- the `SoftwarePotential.Protection-<e30c25>, Version 3.2.1945.4` package contains the protection components that correspond to **version 3.2.1945** of the Permutation with Short Code **e30c25**. (This version resulted from the 4th update to the permutation.)
- the `SoftarePotential.Licensing-MedicalImage_2013, Version 1.0.0.0` package contains **Version 1.0** of the product specification of the **2013 Version** of the **Medical Image** product. (This version indicates this is the initial revision of the product specification.)

In all cases the NuGet package with the highest Version is the most up-to-date, generated by the latest update to either the Permutation or Product definition in your Software Potential service account. 

## View Available Packages

Launch **Package Manager** from your Visual Studio project and select the **Software Potential** source to view a list of packages available in your feed. *[On the first attempt you will be asked to authenticate to the feed using your Software Potential user credentials.  Visual Studio caches these credentials for use in subsequent access attempts]*.

Packages available for installation will display an **Install** button. Packages already installed in your project will have a green tick mark.

You can view the details of a selected package in the right hand panel.  

## Install a Package

Select the package you wish to install and click the **Install** button. By default NuGet Package Manager will attempt to install all packages on which the selected package has a dependency.

## View Installed Packages

In **NuGet Package Manager** select **Installed Packages** to view all packages that you have installed from the feed.

## Uninstall a Package

In **NuGet Package Manager** select **Installed Packages**, then select the package to be uninstalled and click **Uninstall**. 

If the selected package is dependent on one or more packages then you should, when prompted by NuGet Package Manager, also uninstall these packages.

## View Package Updates

Each time you update a Permutation or a Product definition  a new version of the corresponding NuGet packages will be created in the NuGet feed.  In **NuGet Package Manager** select **Updates** to view those packages with updates available for download. Click **Update** to install the updated package.  