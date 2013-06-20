---
title: Licensing-README
---
Welcome to the SoftwarePotential.Licensing-&lt;MyProduct&gt;_&lt;MyVersion&gt;> NuGet Package for your Product

IMPORTANT: this package is intended to be used in concert with a SoftwarePotential.Protection-&lt;PermutationShortCode&gt; NuGet package, which must be installed for the code to compile correctly

The code in this README is intended to provide a terse introduction to some key concepts employed in the Licensing APIs we expose.

It is NOT intended as a full product walkthrough or as a sample of a real application. Please refer to:

- <a href="https://github.com/SoftwarePotential/samples">https://github.com/SoftwarePotential/samples</a> for example code
- <a href="http://support.inishtech.com/">http://support.inishtech.com/</a> for the forum and other support materials

KEY NAMESPACES
==============
    using Sp.Agent; // Most Sp.Agent licensing APIs live in this namespace
    using Slps.ProtectionAttributes; // The [Protect] attribute lives in here
    using System.Linq; // Many of our APIs yield collections etc. that are intended to work well with LINQ

ATTRIBUTES FOR PROTECTION ONLY 
=====================================================================================================
(Provided by accompanying SoftwarePotential.Protection NuGet Package)
0. Protecting code 
------------------
    // (Actually provided by the SoftwarePotential.Protection NuGet Package, but commonly used together with License Checks)
    [Protect] // Protect the code (but no License checks)
    public static void MySensitiveMethod()
    {
    	// Code Protector will Protect the method from ILDASM/ILSpy/Reflector etc. by transforming the code
    	// No licensing restrictions are imposed on running this code
    	// (This Attribute lives in Sp.Agent.dll in the namespace Slps.ProtectionAttributes - it is included in the Sp.Agent NuGet Package but it is typically installed via by the 'Sp.Protection-<PermutationShortCode> NuGet Package )
    }
    
ATTRIBUTES FOR LICENSING AND PROTECTION
=======================================

1. Requiring a Valid product license to run some code
-----------------------------------------------------
    [MyProduct_1.License] // Protect the code + require a license for the product
    public static void MyMethodSubjectToHoldingAnyValidLicense()
    {
    	// Entry to this method is subject to a valid license for the product being available at each execution of the method
    	// (as with [Protect], Code Protector will Protect the method)
    	// (This Attribute is generated into a SpProduct.cs file by the 'Sp.Product-<ProductName>_<ProductVersion> NuGet Package)
    }

2. Requiring a specific Feature from the Software Potential Product Definition to be Available
----------------------------------------------------------------------------------------------
    [MyProduct_1.Features.GlobalAnalysis]
    public static void MyMethodSubjectToAvailabilityOfASpecificFeatureOnTheIssuedLicense()
    {
    	// Entry to this method is subject to the associated Product Feature being included in the Issued License
    	// Both the License and the Feature must be Valid at the time of execution of the method
    	// (as with [Protect], Code Protector will Protect the method)
    	// (For each Feature that has been defined for the Product on the Software Potential Service, there will be an associated attribute generated into the SpProduct.cs file by the 'Sp.Product-<ProductName>_<ProductVersion> NuGet Package)
    }

EXAMPLE CODE USING Sp.Agent APIs TO IMPLEMENT PROGRAMMATIC LICENSE CHECKS
=========================================================================

1. Querying License State
-------------------------
    // Example using Sp.Agent APIs to programmatically determine whether a Licensing Requirement can be fulfilled
    // The licensing check is enforced at the point where the Licensed behavior is actually triggered
    // Here we use the API to enable buttons/menu items and/or offer tooltips or upgrade or 'buy now' links
    public ScreenDefinition GenerateMenu()
    {
    	// This single call will determine the set of currently available features this instant (including Feature Expiration checks etc.)
    	var features = SpAgent.Product.Features.ValidAvailable();
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

2a. Using Licensing State To Guide Application Flow
--------------------------------------------------
    // Example of a case where the behavior of some logic needs to be aware of the Current Licensing state
    // In this example, we have elected not to [Protect] this method. This might be on the basis that
    // - the work we are performing is not intellectually sensitive 
    // - the code should run at peak performance. 
    public ReportOutput GenerateReport()
    {
    	var result = GenerateBaseReportOutput();
    	if ( SpAgent.Product.Features.ValidAvailableContains( MyProduct_1.Features.GlobalAnalysis.Name ) )
    	{
    		var data = LoadDataForGlobalAnalysis();
    		for ( int i = 0; i < 10000; i++ )
    			data.RunPreparationPass( i );
    		// the method RunGlobalAnalysis will typically (directly or indirectly) contain Declarative License checks (i.e., as used on MyMethodSubjectToAvailabilityOfASpecificFeatureOnTheIssuedLicense) in relevant areas
    		result.AddAnalysis( RunGlobalAnalysis( data ) );
    	}
    	return result;
    }

2b. Protecting High Level Application Flow Logic
------------------------------------------------
NOTE: If alternately one considered the logic in GenerateReport() (above) to be sensitive, one might


1. Extract the section doing the 10000 iterations as another Method
2. Apply either one or the other of the following protection attributes
	
	a) **[Protect]** - if you consider the general flow logic to be sensitive (but have placed appropriate License checks / attributes within code being triggered by this controlling logic) OR

	b) **[MyProduct_1.License]** - if, in addition to (a) you also consider it critical that a Valid License is held to be able to run this controlling logic

3. Provisioning/Upgrading/Warning about Expiration of Licenses etc.
-------------------------------------------------------------------
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
    			var userHasLicenseIncludingAdvancedFeature = SpAgent.Product.Features.ValidAvailable().Contains( MyProduct_1.Features.GlobalAnalysis.Name );
    			TriggerStartupBannerWithTeaserForAdvancedModulesIfNotYetUpgraded( userHasLicenseIncludingAdvancedFeature );
    		}
    	}
    	else
    		TriggerNavigateToLicensePurchaseOrActivationScreen();
    }
