Welcome to the SoftwarePotential.Protection-&lt;PermutationShortCode&gt; NuGet Package for your Permutation

NOTE: Licensing aspects of the Software Potential solution are covered in the Licensing README within the SoftwarePotential.Licensing-&lt;MyProduct&gt;_&lt;MyVersion&gt; NuGet Package for your Product

The code in this README is intended to provide a terse introduction to some key concepts related to Code Protection.

It is NOT intended as a full product walkthrough or as a sample of a real application. Please refer to:

- https://github.com/SoftwarePotential/samples/ for example code
- http://support.inishtech.com for the forum and other support materials

##1. Protecting code 
    
    using Slps.ProtectionAttributes; // The [Protect] attribute lives in here
    
    [Protect] // Protect the code (but no License checks)
    public static void MySensitiveMethod()
    {
    // Code Protector will Protect the method from ILDASM/ILSpy/Reflector etc. by transforming the code
    // No licensing restrictions are imposed on running this code
    // (This Attribute lives in Sp.Agent.dll in the namespace Slps.ProtectionAttributes - it is included in the Sp.Agent NuGet Package (which this package depends on)
    }
    

##2. Switching Protection off for some Build Configurations
It is possible to prevent the Code Protection tool from being invoked as part of the build cycle and thus neutralize the effect of adding this package by adding the symbol **;SKIP\_SP\_PROTECT** to your Conditional Compilation Symbols in your Project options.
The obvious detriment of this approach is that you'll no longer receive feedback as to whether code you're marking [Protect] contains constructs that cannot be protected. For this reason it is not generally recommended practice to do this.
If you wish to do this, in Visual Studio: 
 1. on the Project menu, select the &lt;ProjectName&gt; Properties menu option
 2. on the Build page, in the General selection, add **;SKIP_SP_PROTECT** to the end of the 'Conditional compilation symbols'

##3. Sp.Protect.config
This package adds a file called Sp.Protect.config to the root folder of your project. This file is used to convey settings to the Code Protector tool that runs as part of the build.
Note that this file can also be used with Code Protector GUI tool which is available as part of the Software Potential SDK (this is a separate download from your Account on https://srv.softwarepotential.com).

4. Examining Errors when Protection Fails
-----------------------------------------
Note that typically if protection fails, you'll get an error like:
**ERROR: &lt;todo get a representative output message&gt;**
The actual details of the problem with full context are available by viewing the Build Output. This is available via the View|Output menu option in Visual Studio (Commonly available via Ctrl-Alt-O) - be sure to select 'Build' in the 'Show output from' dropdown.

5. Switching to auto-exclusion mode for methods containing unsupported constructs
---------------------------------------------------------------------------------
The default behavior of the sytem is to Fail Fast when a method cannot be protected. In this mode the build is aborted if any method fails to be protected but each failure will be reported.
In some cases (e.g. if you are shortlisting a set of methods to be protected and want to repeatedly run during refactoring of the code as part of this process), one may prefer to switch to the mode where failures are reported but an automated re-run takes place with the methods that failed simply left unprotected.
To turn on automatic retry excluding methods with unsupported methods from protection, apply the following setting in your Sp.Protect.config:
	**&lt;SuppressAutomaticMethodExclusion&gt;false
&lt;/SuppressAutomaticMethodExclusion&gt;**
