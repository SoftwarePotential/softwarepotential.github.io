Welcome to the SoftwarePotential.Configuration.Local.MultiUser-&lt;PermutationShortCode&gt; NuGet Package.

IMPORTANT: this package is intended to be used in concert with:
* SoftwarePotential.Licensing-&lt;MyProduct&gt;-&lt;MyVersion&gt; NuGet package (must be installed for the code to compile correctly)
* SoftwarePotential.Protection-&lt;PermutationShortCode&gt; NuGet package

The code in this README is intended to provide a terse introduction to some key techniques related to configuring Software Potential-licensed applications, which  are meant to be used by multiple Windows users on a single machine.  (If your application is intended to be used by a single user on each machine, consider using SoftwarePotential.Configuration.Local.SingleUser-&lt;PermutationShortCode&gt; NuGet Package instead).

It is NOT intended as a full product walkthrough or as a sample of a real application. Please refer to:
- <https://github.com/SoftwarePotential/samples> for example code
- <http://support.inishtech.com> for the forum and other support materials

Shared license store overview
----------------------------------
In a multi-user scenario, the licenses are stored in a shared application data location, e.g. ``C:\ProgramData\MyCompany\MyProduct\MyVersion``

The license store needs to be accessible by all users of the application on a given machine. 
Therefore an explicit store initialization step is needed as part of application deployment (as outlined in **License Store Initialization** section) ).

LICENSE STORE INITIALIZATION
============================
The shared licensed store needs to be created and initialized by a privileged user (local administrator) during application deployment/installation.
The following approaches are possible:

1.1. Using installutil.exe during installation
-----------------------------------------------
This package provides an `SpAgentInstaller` class (in `SpAgent.Configuration.Local.MultiUser.Installer.cs`), which is a .NET Managed Installer class.
The installation sequence within `SpAgentInstaller` class can be invoked by running installutil.exe command-line utility (provided by .NET Framework SDK or Windows SDK) from Administrator Command Prompt:
``installutil.exe MyApplication.exe``
This command needs to be called from your installer / installation script.

1.2. Employing a command-line installation option in the application
---------------------------------------------------------------------
* Add a command-line installation option to your application (e.g. `-install` )
* When the commandline option is supplied, have your `Main` method:
  + call `SpAgentConfiguration.InitializeSharedLicenseStorage()`
  + exit to allow the rest of the installation to complete (i.e. the application should exit immediately)
* Have your installer / installation script call your application with the installation option:
  ``MyApplication.exe -install``

1.3. Using a custom action in a Windows Installer
------------------------------------------------
For an example how to use a custom action in a Windows Installer to initialize a license store, please refer to this sample:
<https://github.com/SoftwarePotential/samples/tree/master/Sp.Samples.Agent.WpfApplicationWithInstaller>

CONFIGURATION
==============

Verifying that the license store is initialized
------------------------------------------------
We recommend calling this API method from your application's `Main` method/entry point:
`SpAgent.Configuration.VerifyStoresInitialized();`
This verifies that the initialization step outlined in **License Store Initialization** section has been performed.

Customizing license store path
-------------------------------
To change the license store path, you need to modify `SpAgent.ConfigureLocalStorePath()` method (in `SpAgent.Configuration.Local.Customizations.cs`).

TROUBLESHOOTING
===============
Installation time
-----------------
* Sp.Agent.Storage.WritingStorageInaccessibleException
  +  The store initialization needs to run in a privileged user context. Make sure that the process that runs store initialization has proper permissions.
  
Application runtime
--------------------
* Sp.Agent.Storage.WritingStorageInaccessibleException
  +  License Store isn't accessible for writing. Usually it means that the **License Store Initialization** step hasn't been performed.
