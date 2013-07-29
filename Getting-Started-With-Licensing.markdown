---
layout: github.io
category: pages
title: Getting Started - Licensing with Software Potential
tagline: Using Software Potential to License Your Application
---

Welcome to the **Quick Start Guide to Licensing**. The purpose of this document is to get you, a first time user, up and running quickly when using the Software Potential service to license your .NET assembly. 

It is NOT intended as a detailed guide to application licensing or to cover Code Protection aspects. Please refer to:

- [http://www.softwarepotential.com](http://www.softwarepotential.com) if you don't already have a valid account on the Software Potential Service
- [http://docs.softwarepotential.com/Getting-Started-With-Code-Protection.html](http://docs.softwarepotential.com/Getting-Started-With-Code-Protection.html) if you wish to only protect your application
- [http://docs.softwarepotential.com/Protection-README.html](http://docs.softwarepotential.com/Protection-README.html) for more detailed code protection guidance e.g. how to disable protection for a build configuration, troubleshooting protection etc.
- [http://docs.softwarepotential.com/Licensing-README.html](http://docs.softwarepotential.com/Licensing-README.html) for more detailed information about Licensing
- [https://github.com/SoftwarePotential/samples/](https://github.com/SoftwarePotential/samples/) for example code
- [http://support.inishtech.com](http://support.inishtech.com) for the Technical Support forum and other support materials  

# Step 1: Create Your Permutation

A permutation defines a unique one way transformation that Code Protector will use to transform the CIL of your assemblies so that the protected assemblies cannot be reverse engineered. You will need to create  a Permutation that will be used to protect your product prior to being able to protect code:- 

 1. Log in to the Software Potential service at [https://srv.softwarepotential.com](https://srv.softwarepotential.com)
 2. Select **Accounts** -> **Manage Permutations** -> **Create Permutation**
 3. Enter a name for your permutation and click **OK**. (This name is purely a nickname used to identify it on menus should you have multiple permutations in your account. We recommend to use a name related to the product you'll be protecting.) 
 4. It may take a minute or so for your permutation to generate. On successful generation the status field will change from **Creation Pending** to **Done** (NB you'll need to click **Refresh** to update the status).

Each permutation is uniquely identified by its **Short Code** which consists of the first five characters of the `Permutation Id` field.  For example a Permutation whose Id is `e9dc24g07-d195-4658-a312-82132945711d`" has a ShortCode of `e9dc2`

# Step 2: Define Your Product 

To license your application you will need to associate it with a Product defined by you in the Software Potential service. To do this:

 1. Log in to the Software Potential service at [https://srv.softwarepotential.com](https://srv.softwarepotential.com)
 2. Select **Products** -> **Add Product**
 3. Add a **Name**, **Version** and a **Description** (optional) for the product, and click **OK** to save. You should now see the new product listed in the **Products** page.

Once your Product is defined you are now ready to license your application such that only those users with a valid license can run it.

## Define Product Features (optional)
If you wish to employ feature-based licensing (and your subscription permits this) you may wish to add Features to your new product as follows:

 1. Select your new Product in the list of available products and click **Edit**
 2. Enter a **Name** and (optionally) a **Description** for the feature, and the click the **+** icon to add the feature to the product.
 3. Repeat this for each feature you wish to add to the product.

Once features have been defined you are now in a position to license individual pieces of functionality within your application, such that only those users with a valid license containing the required feature can access the corresponding functionality. 

# Step 3: Install the NuGet Packages for Licensing
Once you have generated a Permutation and defined a Product in the Software Potential service, the Software Potential components required to license and protect your .NET application are delivered as a set of NuGet packages via the Software Potential NuGet feed at [https://srv.softwarepotential.com/NuGet/nuget](https://srv.softwarepotential.com/NuGet/nuget).

## Software Potential NuGet Packages for Declarative Licensing
To each project in your Visual Studio solution where you wish to both license AND protect code you will need to add the following packages:

* **SoftwarePotential.Licensing-&lt;Product&gt;_&lt;Version&gt;** e.g. SoftwarePotential.Licensing-MedicalImageViewer-2013
* **SoftwarePotential.Protection\-&lt;PermutationShortCode&gt;** e.g. SoftwarePotential.Protection-e9dc2.


In the root (or StartUp) project of your solution you will also need to add a **SoftwarePotential.Configuration.Local.&lt;SingleUser/MultiUser&gt;-&lt;PermutationShortCode&gt;** package to configure a local license store appropriate to your application type i.e. either 

* **SoftwarePotential.Configuration.Local.SingleUser-&lt;PermutationShortCode&gt;** - if your application does not need to be accessible to multiple user profiles on a given machine and/or you do not have an elevated installation process (For more details see [Configuration.Local.SingleUser-README.html](http://docs.softwarepotential.com/Configuration.Local.SingleUser-README.html)), 

OR

* **SoftwarePotential.Configuration.Local.MultiUser-&lt;PermutationShortCode&gt;** - if your application needs to be accessible to multiple user profiles on a given machine and have an installation process that can run elevated (For more details see [Configuration.Local.MultiUser-README.html](http://docs.softwarepotential.com/Configuration.Local.MultiUser-README.html))

## Software Potential NuGet Packages for Programmatic Licensing
In some cases, one may wish to query licenses and/or other Agent API calls without having this code protected e.g. for performance reasons. In this case, you can avoid having a Code Protection phase for each such project by using the **SoftwarePotential-&lt;PermutationShortCode&gt;**package in place of the **SoftwarePotential.Protection\-&lt;PermutationShortCode&gt;**

## Registering the Software Potential NuGet endpoint in Visual Studio

If you have not already done so you will need to add the Software Potential NuGet source to your list of Online sources in NuGet Package Manager in Visual Studio - see [Adding Software Potential NuGet Feed](http://docs.softwarepotential.com/Adding-SoftwarePotential-NuGet-Feed.html) 
 
## Installing the Packages

To install the packages required to protect and license code in a Visual Studio project:

 1. Right click the Visual Studio project in **Solution Explorer** and select the **Manage NuGet Packages** option.
 2. In Package Manager select the **Software Potential** source from the list of **Online sources** (to view only the Software Potential packages). (See *Registering the Software Potential NuGet endpoint in Visual Studio* above if you do not have such an endpoint registered yet)
 3. Select the package **SoftwarePotential.Licensing-&lt;Product&gt;_&lt;Version&gt;** and click  the **Install** button to install in your project.
 4. Repeat for the other packages as required.  

Once the above packages are installed, the Code Protector tooling will be invoked on the output assembly as part of each project build by default, with the result that methods you identify can be automatically licensed (or just protected) each time it is built. Please see [SoftwarePotential.Licensing-README](http://http://docs.softwarepotential.com/Licensing-README.html) for more detailed licensing guidance e.g. how to disable declarative licensing/protection on a build configuration, how to access license data programmatically etc.

# Step 4: Configure Licensing Of Your Application

The Software Potential runtime components make no assumptions about your Application i.e., where licenses are stored is completely defined by code within your application. Regardless of what kind of application you have, you'll need to identify your desired configuration to the Software Potential runtime components. 

When you installed the SoftwarePotential.Configuration NuGet package in Step 3 you selected the store configuration appropriate to your application type. (Please refer to relevant [Configuration README](http://docs.softwarepotential.com/index.html) for further information on how to customize the store configuration in your application code if required.)

## Stores requiring an Installation Step

Depending on your application type and the licensing models to be supported, your application's installation process may need to provision and initialize a license store. Please refer to [Configuration.Local.MultiUser-README.html](http://docs.softwarepotential.com/Configuration.Local.MultiUser-README.html) for further information about this requirement and the steps involved in applying it to your application. 

## Startup Code: MANDATORY Verification step for all Licensed Applications

In order for the licensing system to know how you wish to manage your licenses, you need to add a call to the Licensing routines that identifies your desired license storage configuration as part of your application's startup. 

This needs to run **before any Protected or Licensed code in your system** (failure to do so makes the system switch into Protected Code only mode). To do this call `SpAgent.Configuration.VerifyStoresInitialized();` in your `Main` or other environment-specific startup hook.

```c#
class Program
{
	static int Main( string[] args )
	{
		try
		{
			SpAgent.Configuration.VerifyStoresInitialized();

			// TODO start your application

			return 0;
		}
		catch ( Exception ex )
		{
			Console.Error.WriteLine( "Exception: " + ex );
			return 1;
		}
	}
}
```

# Step 5: Mark Methods to be Protected or Licensed 

To identify the methods to be licensed in a source file you just need to mark each method with the appropriate `ProtectionAttribute` as per the guidance in [http://http://docs.softwarepotential.com/Licensing-README.html](http://http://docs.softwarepotential.com/Licensing-README.html). 
 
For example, the methods in the following code have been marked for protection or licensing against version **2013** of the product **MyProduct**

```c#
Using System;
using Slps.ProtectionAttributes;

public static class ProtectedCode
{
	public static string UnprotectedAction()
	{
		return "Executed UnprotectedAction";
	}

	// PROTECTED BUT NOT LICENSED
	[Protect]
	public static string ProtectedAction1()
	{
		return "Executed ProtectedAction1";
	}
	
	// PROTECTED AND REQUIRES A VALID LICENSE
	[MyProduct_2013.License]
	public static string ProtectedAction2()
	{
		return "Executed ProtectedAction2";
	}
	
	// PROTECTED AND REQUIRES A VALID LICENSE WITH "FEATURE A"
	[MyProduct_2013.Features.FeatureA]
	public static string ProtectedAction3()
	{
		return "Executed ProtectedAction3";
	}
}
```
Please refer to [SoftwarePotential.Licensing-README](http://http://docs.softwarepotential.com/Licensing-README.html) on how to implement licensing programmatically i.e using the Software Potential runtime licensing API's directly. 

# Step 6: Add facilities for Activation and License Management to your Application
To complete the licensing of your application you will need to enable your customers to activate licenses and manage significant licensing events e.g. when there is no license to run the application.
For more implementation details see the sample applications at [https://github.com/SoftwarePotential/samples/](https://github.com/SoftwarePotential/samples/)
 
## Handling NotLicensedException
When an attempt is made to execute code for which an appropriate license is not available, the runtime will throw a `NotLicensedException`. Typically you will handle this in your application by 

* Displaying an error message to inform the user that there is no license available to execute the required functionality
* Offering the user the opportunity to purchase/upgrade/activate a license to enable the licensed feature
The exact way in which this is best handled will depend on the particulars of your application and its operating environment. The samples illustrate typical approaches to this.

## Capturing an Activation Key to pass to the License Activation API
In order to run a licensed application the user will need to activate a license via the `SpAgent.Product.Activation` API, passing an activation key. Typically such a key is captured in a UI element within the application itself. The following code sample illustrates one implementation approach (which is used in our WPF sample application): 

```c#
public class ActivationModel : IDataErrorInfo, INotifyPropertyChanged
{
	string _activationKey;

	public string ActivationKey
	{
		get { return _activationKey; }
		set { _activationKey = value; OnPropertyChanged( "ActivationKey" ); }
	}

	// Validation logic wired in by Binding in the XAML for the form to give a clear message to the user
	static string ValidateActivationKey( string activationKey )
	{
        if ( !SpAgent.Product.Activation.IsWellFormedKey( activationKey ) )
		    return "The activation key is invalid - please ensure digits/letters have been entered correctly in XXXXX-XXXXX-XXXXX-XXXXX-XXXXX format";
		return null;
	}

	// XAML sets this as max text input length for Activation Key entry element
	public static int ActivationKeyRequiredLength
	{
		get { return 29; }
	}

	// XAML wires this to the OK Button Click action
	public void OnlineActivate()
	{
		SpAgent.Product.Activation.OnlineActivate( ActivationKey );
	}
}
```

## Displaying License Details, Managing trials and upgrades
Please refer to [https://github.com/SoftwarePotential/samples/](https://github.com/SoftwarePotential/samples/) and [http://docs.softwarepotential.com/Licensing-README.html](http://docs.softwarepotential.com/Licensing-README.html) for examples of using the APIs to surface information as to the installed licenses etc.

# Step 7: Build Your Application
Build your application as normal (in debug or release mode). Your output assemblies in your normal build output directory (i.e. `bin\debug` or `bin\release`) should now have the relevant methods as identified in Step 4 be protected and/or subject to license verification. Relevant accompanying runtime libraries added by the NuGet packages will also be in the output directory and should also be deployed alongside the application.