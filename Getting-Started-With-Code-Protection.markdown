---
layout: github.io
category: pages
title: Getting Started - Code Protection with Software Potential 
tagline: Using Software Potential to Protect Your Application
---

Welcome to the **Quick Start Guide to Code Protection**. The purpose of this document is to get you, a first time user, up and running quickly when using the Software Potential service to protect your .NET assembly.

It is NOT intended as a detailed guide to Code Protection or to cover Licensing aspects. Please refer to:

- [http://www.softwarepotential.com](http://www.softwarepotential.com) if you don't already have a valid account on the Software Potential Service 
- [http://docs.softwarepotential.com/Protection-README.html](http://docs.softwarepotential.com/Protection-README.html) for more detailed code protection guidance e.g. how to disable protection for a build configuration, troubleshooting protection etc.
- [http://docs.softwarepotential.com/Getting-Started-With-Licensing.html](http://docs.softwarepotential.com/Getting-Started-With-Licensing.html) if you wish to license your application
- [http://docs.softwarepotential.com/Licensing-README.html](http://docs.softwarepotential.com/Licensing-README.html) for more information about Licensing
- [https://github.com/SoftwarePotential/samples/](https://github.com/SoftwarePotential/samples/) for example code
- [http://support.inishtech.com](http://support.inishtech.com) for the Technical Support forum and other support materials 
 
# Step 1: Create Your Permutation

A permutation defines a unique one way transformation that Code Protector will use to transform the CIL of your assemblies so that the protected assemblies cannot be reverse engineered. You will need to create Permutation that will be used to protect your product prior to being able to protect code:- 

1. Log in to the Software Potential service at [https://srv.softwarepotential.com](https://srv.softwarepotential.com)
2. Select **Accounts** -> **Manage Permutations** -> **Create Permutation**
3. Enter a name for your permutation and click **OK**. (This name is purely a nickname used to identify it on menus should you have multiple permutations in your account. We recommend to use a name related to the product you'll be protecting.) 
4. It may take a minute or so for your permutation to generate. On successful generation the status field will change from **Creation Pending** to **Done** (NB you'll need to click **Refresh** to update the status).

Each permutation is uniquely identified by its **Short Code** which consists of the first five characters of the `Permutation Id` field.  For example a Permutation whose Id is `e9dc24g07-d195-4658-a312-82132945711d`" has a ShortCode of `e9dc2`

# Step 2: Install Code Protection NuGet Packages 

The Software Potential components required to protect your .NET application are delivered as a set of NuGet packages via the Software Potential NuGet feed at [https://srv.softwarepotential.com/NuGet/nuget](https://srv.softwarepotential.com/NuGet/nuget).

Each time a new Permutation is generated (or an existing Permutation is updated) a set of corresponding NuGet packages is uploaded to the Software Potential NuGet feed. 

For a given permutation identified by its **Short Code** the package required to protect your application is listed as
**SoftwarePotential.Protection\-&lt;PermutationShortCode&gt;** e.g. `SoftwarePotential.Protection-e9dc2`.

## Registering the Software Potential NuGet endpoint in Visual Studio

If you have not already done so you will need to add the Software Potential NuGet source to your list of Online sources in NuGet Package Manager in Visual Studio - see [Adding Software Potential NuGet Feed](http://docs.softwarepotential.com/Adding-SoftwarePotential-NuGet-Feed.html) 

## Installing the Packages

To install the packages required to protect code in a Visual Studio project:

 1. Right click the Visual Studio project in **Solution Explorer** and select the **Manage NuGet Packages** option.
 2. In Package Manager select the **Software Potential** source from the list of **Online sources** (to view only the Software Potential packages). (See *Registering the Software Potential NuGet endpoint in Visual Studio* above if you do not have such an endpoint registered yet)
 3. Select the **SoftwarePotential-Protection-&lt;PermutationShortCode&gt;** package (where *PermutationShortCode* is that of the permutation you generated in Step 1 above) and click  the **Install** button to install in your project.  

Once the package is installed, the Code Protector will be invoked on the project as part of each project build by default. Please see [SoftwarePotential.Protection-README](http://http://docs.softwarepotential.com/Protection-README.html) for more detailed code protection guidance e.g. how to disable protection for a build configuration, how to troubleshoot protection issues etc.

# Step 3: Select methods to be protected using Protection Attributes

To identify methods to be protected you just mark each method with a `[Protect]` attribute.  For example:

```c#
Using System;
using Slps.ProtectionAttributes;

namespace Sp.Samples.ProtectDemo
{
	public static class ProtectedCode
	{
		public static string UnprotectedAction()
		{
			return "Executed UnprotectedAction";
		}

		[Protect]
		public static string ProtectedAction()
		{
			return "Executed ProtectedAction";
		}
	}
}
```

# Step 4: Build Your Application
Build your application as normal (in debug or release mode). The protected assemblies (together with the Software Potential DLLs required to execute protected code) will be in the appropriate build output directory (i.e. `bin\debug` or `bin\release`).

# Step 5: Finished - confirming the results
The preceding steps should have resulted in your application being correctly protected as part of every build.

If you wish you can load the assemblies into a tool such as [ILSpy](http://ilspy.net/) to confirm that the methods have been protected by examining the methods. In the case of the earlier example the `ProtectedAction()` method is transformed to:

```c# 
// Sp.Samples.ProtectedDemo.ProtectedCode
private static void ProtectedAction()
{
	var args = new object[0];
	PermutedExecutionServices2.ExecuteMethod( null,
        "3dc39cecaf7e421bb8c67658abc54dd1",
        "0505580B696A45BE879E19F8EA165DEA", args, null, null);
}
```