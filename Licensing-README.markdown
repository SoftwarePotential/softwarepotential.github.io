---
layout: github.io
category: pages
title: Licensing-README
tagline: Software Potential Licensing NuGet package README
---

Welcome to the **SoftwarePotential.Licensing-&lt;MyProduct&gt;_&lt;MyVersion&gt;** NuGet Package for your Product

**NOTE:** The code protection aspects of the Software Potential solution are covered in the [Protection-README](http://docs.softwarepotential.com/Protection-README.html) within the SoftwarePotential.Protection-&lt;PermutationShortCode&gt; NuGet Package for your Permutation.

The code in this README is intended to provide a terse introduction to some key concepts employed in the Licensing APIs we expose. It is NOT intended as a full product walkthrough or as a sample of a real application. Please refer to:

- [https://github.com/SoftwarePotential/samples](https://github.com/SoftwarePotential/samples) for example code
- [http://support.inishtech.com/](http://support.inishtech.com/) for the Technical Support forum and other support materials

# IMPORTANT: Implicit Package Dependencies REQUIRED TO MAKE THE CODE COMPILE
This package is intended to be used in conjunction with other packages, which ***must be installed for the code to compile correctly***.

1. (always) a package that will add runtime components (from your Permutation):-
 * For your application's **main project/Composition Root responsible for managing the licensing configuration** add an appropriate *Configuration package*, i.e. one of the [**SoftwarePotential.Configuration.&lt;ApplicationStyle&gt;-&lt;PermutationShortCode&gt;** NuGet packages](http://docs.softwarepotential.com/index.html) 
 * for **other projects** (e.g. if you have more than one project that uses Licensing) add the **SoftwarePotential-&lt;PermutationShortCode&gt;** package to each other project that needs access to product-relative functionality)
2. (typically) a *Protection package*, i.e. add a [**SoftwarePotential.Protection-&lt;PermutationShortCode&gt;** NuGet package](http://docs.softwarepotential.com/Protection-README.html) to any projects in which you wish to apply protection

# IMPORTANT: Key Code Changes in Your Application To Support Licensing 

The Software Potential runtime components make no assumptions about your Application i.e., where licenses are stored is completely defined by code within your application.

If you are just Protecting your code, the system doesn't require any further information and can work without any code changes. This is *not* the case for Licensed applications.

## Selecting a Store Configuration

Regardless of what kind of application you have, you'll need to identify your desired configuration to the Software Potential runtime components. There are a number of SoftwarePotential.Configuration NuGet packages available, or you can opt to call the APIs directly. 

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

# Attributes For Protecting Code Without Requiring A License 
(Provided by accompanying **SoftwarePotential.Protection-&lt;PermutationShortCode&gt;** NuGet package. See the [Protection README](http://docs.softwarepotential.com/Protection-README.html) for more information.)


```c#
// (Actually provided by the SoftwarePotential.Protection NuGet Package, but commonly used together with License Checks)
[Protect] // Protect the code (but no License checks)
public static void MySensitiveMethod()
{
    // Code Protector will Protect the method from ILDASM/ILSpy/Reflector etc. by transforming the code
    // No licensing restrictions are imposed on running this code
    // (This Attribute lives in Sp.Agent.dll in the namespace Slps.ProtectionAttributes - it is included in the Sp.Agent NuGet Package but it is typically installed via by the 'Sp.Protection-<PermutationShortCode> NuGet Package )
}
```
    
# Attributes For Licensing And Protection

## Requiring a Valid product license to run some code

```c#
[MyProduct_1.License] // Protect the code + require a license for the product
public static void MyMethodSubjectToHoldingAnyValidLicense()
{
    // Entry to this method is subject to a valid license for the product being available at each execution of the method
    // (as with [Protect], Code Protector will Protect the method)
    // (This Attribute is generated into a SpProduct.cs file by the 'Sp.Product-<ProductName>_<ProductVersion> NuGet Package)
}
```

## Requiring a specific Feature from the Software Potential Product Definition to be Available

```c#
[MyProduct_1.Features.GlobalAnalysis]
public static void MyMethodSubjectToAvailabilityOfASpecificFeatureOnTheIssuedLicense()
{
    // Entry to this method is subject to the associated Product Feature being included in the Issued License
    // Both the License and the Feature must be Valid at the time of execution of the method
    // (as with [Protect], Code Protector will Protect the method)
    // (For each Feature that has been defined for the Product on the Software Potential Service, there will be an associated attribute generated into the SpProduct.cs file by the 'Sp.Product-<ProductName>_<ProductVersion> NuGet Package)
}
```
# Example Code Using Sp.Agent Apis To Implement Programmatic License Checks

## Querying License State

```c#
// Example using Sp.Agent APIs to programmatically determine whether a Licensing Requirement can be fulfilled
// The licensing check is enforced at the point where the Licensed behavior is actually triggered
// Here we use the API to enable buttons/menu items and/or offer tooltips or upgrade or 'buy now' links
public ScreenDefinition GenerateMenu()
{
    // This single call will determine the set of currently available features this instant (including Feature Expiration checks etc.)
    var features = SpAgent.Product.Features.Valid();
    return new ScreenDefinition {
    	new Title ( "Main Menu"),
    	new Button ( 
    		"Run Report", 
    		OnClick = () => ShowReportViewer(GenerateReport())),
    	new Button ( 
    		"Run Basic Check", 
    		OnClick = MySensitiveMethod),
    	new Button ( 
    		"Run Detailed Analysis", 
    		Enabled = features.Any(),
    		OnClick = MyMethodSubjectToHoldingAnyValidLicense),
    	new Button ( 
    		"Run Global Analysis", 
    		Enabled = features.Contains( MyProduct_1.Features.GlobalAnalysis.Name ),
    		OnClick = MyMethodSubjectToAvailabilityOfASpecificFeatureOnTheIssuedLicense),
    	new Button ( 
    		"Run Global Optimization", 
    		Enabled = features.Contains( MyProduct_1.Features.GlobalOptimization.Name ),
    		OnClick = StartGlobalOptimizationProcess)
    };
}
```

## Using Licensing State To Guide Application Flow

```c#
// Example of a case where the behavior of some logic needs to be aware of the Current Licensing state
// In this example, we have elected not to [Protect] this method. This might be on the basis that
// - the work we are performing is not intellectually sensitive 
// - the code should run at peak performance. 
public ReportOutput GenerateReport()
{
    var result = GenerateBaseReportOutput();
    if ( SpAgent.Product.Features.ValidContains( MyProduct_1.Features.GlobalAnalysis.Name ) )
    {
    	var data = LoadDataForGlobalAnalysis();
    	for ( int i = 0; i < 10000; i++ )
    		data.RunPreparationPass( i );
    	// the method RunGlobalAnalysis will typically (directly or indirectly) contain Declarative License checks (i.e., as used on MyMethodSubjectToAvailabilityOfASpecificFeatureOnTheIssuedLicense) in relevant areas
    	result.AddAnalysis( RunGlobalAnalysis( data ) );
    }
    return result;
}
```
### Protecting High Level Application Flow Logic

NOTE: If alternately one considered the logic in `GenerateReport()` (above) to be sensitive, one might

1. Extract the section doing the 10000 iterations as another Method
2. Apply either one or the other of the following protection attributes
	
	a) **\[Protect\]** - if you consider the general flow logic to be sensitive (but have placed appropriate License checks / attributes within code being triggered by this controlling logic) OR

	b) **\[MyProduct_1.License\]** - if, in addition to (a) you also consider it critical that a Valid License is held to be able to run this controlling logic

## Provisioning/Upgrading/Warning about Expiration of Licenses etc.

```c#    
// Example of some basic logic using the Sp.Agent APIs to manage evaluations / purchases / upgrades of licenses for our product
// One should consider running this as an asynchronous or background activity, as the first call to a licensing routine in a cold application should ideally not unnecessarily impede the users flow
[Protect] // Generally one would wish to Protect code such as this, as decompilation might unnecessarily leak information as to how your Licensing strategy as a whole works
void CoordinateLicenseUpgrades()
{
    //// Before this, one should have 
    //// a) Configured and your License storage (typically during installation) 
    //// b) Verified that License Storage is correctly configured
    //// The details of how this is coordinated depend on the specific Store and Application Type.
    //// See https://github.com/SoftwarePotential/samples for example code
    var validLicenses = SpAgent.Product.Licenses.Valid();
    if ( validLicenses.Any() )
    {
    	var expiringFeatures = SpAgent.Product.Features.ValidAvailableExpiringWithin( TimeSpan.FromDays( 30 ) );
    	if ( expiringFeatures.Any() )
    		TriggerLicensesExpiringWarningPage( expiringFeatures, validLicenses );
    	else
    	{
    		var userHasLicenseIncludingAdvancedFeature = SpAgent.Product.Features.ValidContains( MyProduct_1.Features.GlobalAnalysis.Name );
    		TriggerStartupBannerWithTeaserForAdvancedModulesIfNotYetUpgraded( userHasLicenseIncludingAdvancedFeature );
    	}
    }
    else
    	TriggerNavigateToLicensePurchaseOrActivationScreen();
}
```

# Key Namespaces

```c#
using Sp.Agent; // Most Sp.Agent licensing APIs live in this namespace
using Slps.ProtectionAttributes; // The [Protect] attribute lives in here
using System.Linq; // Many of our APIs yield collections etc. that are intended to work well with LINQ
```
