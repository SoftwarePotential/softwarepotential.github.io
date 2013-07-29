---
layout: github.io
category: pages
title: SoftwarePotential.Configuration.Local.MultiUser-README
tagline: README for SoftwarePotential.Configuration.Local.MultiUser NuGet package from Software Potential
---

Welcome to the SoftwarePotential.Configuration.Local.MultiUser-&lt;PermutationShortCode&gt; NuGet Package.

IMPORTANT: this package is intended to be used in concert with:

* SoftwarePotential.Licensing-&lt;MyProduct&gt;-&lt;MyVersion&gt; NuGet package (must be installed for the code to compile correctly). See [the associated Licensing README](http://docs.softwarepotential.com/Licensing-README.html) for more information.
* SoftwarePotential.Protection-&lt;PermutationShortCode&gt; NuGet package. See [the associated Protection README](http://docs.softwarepotential.com/Protection-README.html) for more information.

# Key Constraints and Advantages of this MULTI USER Configuration Package

This package is intended to offer a licensing configuration for an application that needs to manage licenses across multiple user Profiles. This means your application and its licenses **can be used by multiple Windows users on each target machine**. It also means you must write and distribute a separate installer (i.e. an MSI package or similar) to initialize the required license store.
(See [the SingleUser equivalent](http://docs.softwarepotential.com/Configuration.Local.SingleUser-README.html) if your application is intended to be used by a single user on each machine.) 

# License Storage Location Base Path

Adding this NuGet package to your application configures licenses to be stored in a shared Application Data folder i.e. out of the box, the directory used by the code in this package results in a base path such as ``C:\ProgramData\MyCompany\MyProduct\MyVersion``

The license store needs to be accessible by all users of the application on a given machine. Therefore an explicit store initialization step is needed as part of application deployment (as outlined in **License Store Initialization** section) ).

# Customizing the license store path

Out of the box, the path used is derived from your Company and Product names as supplied to the Software Potential service. This is only intended as a placeholder and you are free to customize the exact names used.

Within the Base Path, this package generates a subdirectory structure for your applications licenses as follows: ``MyCompany\MyProduct\MyVersion`` (Having a subdirectory structure like this is necessary if the base directory is shared among all applications on the machine.)

To change either the base path, or the subdirectory used for your application's license storage area, please refer to the `SpAgent.ConfigureLocalStorePath()` method (in `SpAgent.Configuration.Local.Customizations.cs`).

# License Store Initialization

The shared licensed store needs to be created and initialized by a privileged user (local administrator) during application deployment/installation. The following approaches are possible:

## 1.1. Using installutil.exe during installation

This package provides an `SpAgentInstaller` class (in `SpAgent.Configuration.Local.MultiUser.Installer.cs`), which is a .NET Managed Installer class.
The installation sequence within `SpAgentInstaller` class can be invoked by running installutil.exe command-line utility (provided by .NET Framework SDK or Windows SDK) from Administrator Command Prompt:
``installutil.exe MyApplication.exe``
This command needs to be called from your installer / installation script.

## 1.2. Employing a command-line installation option in the application

* Add a command-line installation option to your application (e.g. `-install` )
* When the commandline option is supplied, have your `Main` method:
  + call `SpAgentConfiguration.InitializeSharedLicenseStorage()`
  + exit to allow the rest of the installation to complete (i.e. the application should exit immediately)
* Have your installer / installation script call your application with the installation option:
  ``MyApplication.exe -install``

## 1.3. Using a custom action in a Windows Installer

For an example how to use a custom action in a Windows Installer to initialize a license store, please refer to this sample:
[https://github.com/SoftwarePotential/samples/tree/master/Sp.Samples.Agent.WpfApplicationWithInstaller](https://github.com/SoftwarePotential/samples/tree/master/Sp.Samples.Agent.WpfApplicationWithInstaller)

# License Store Verification

We recommend calling this API method from your application's `Main` method/entry point:

`SpAgent.Configuration.VerifyStoresInitialized();`

This verifies that the initialization step outlined in **License Store Initialization** section has been performed.


# Troubleshooting

The key requirement of this configuration is that your application and installer are run with sufficient privilege to

- create the base directory (e.g. C:\ProgramData)
- create folders within the base directory (e.g. MyCompany\MyProduct\MyVersion
- write files within the above area

Any failures will result in an Exception derived from ``StorageInaccessibleException``.

## Installation time

* `Sp.Agent.Storage.WritingStorageInaccessibleException` - The store initialization needs to run in a privileged user context. Make sure that the process that runs store initialization has proper permissions.
  
## Application runtime

* `Sp.Agent.Storage.WritingStorageInaccessibleException`- License Store isn't accessible for writing. Usually it means that the **License Store Initialization** step hasn't been performed.
