---
layout: github.io
category: pages
title: SoftwarePotential.Configuration.Distributor-README
tagline: README for SoftwarePotential.Configuration.Distributor NuGet package from Software Potential
---

Welcome to the SoftwarePotential.Configuration.Distributor NuGet Package.

**IMPORTANT:** this package is intended to be used in concert with:

* SoftwarePotential.Licensing-&lt;MyProduct&gt;-&lt;MyVersion&gt; NuGet package (must be installed for the code to compile correctly). See [the associated Licensing README](http://docs.softwarepotential.com/Licensing-README.html) for more information.
* SoftwarePotential.Protection-&lt;PermutationShortCode&gt; NuGet package. See [the associated Protection README](http://docs.softwarepotential.com/Protection-README.html) for more information.

# Purpose of this Distributor Configuration Package

This package adds relevant methods and properties into the `SpAgent` class (from your SoftwarePotential.Licensing-&lt;MyProduct&gt;-&lt;MyVersion&gt; NuGet package) which you can employ to customize applications that are intended to work with floating/concurrent licenses managed by one or more Distributor instances installed on the end customer's premises.

When Distributor is used, the licenses are activated on the Distributor instance and licensed applications that wish to avail of floating/concurrent licenses must be configured to connect to the Distributor instance. Please see [Getting Started - Licensing with Distributor]() for more details on how to use Distributor to implement floating/concurrent licensing models in your application.

An application with a Distributor configuration can use Local and Distributor licenses in parallel. SpAgent will attempt to utilize any available local licenses before attempting to request resources from the Distributor instance.

# Distributor Base Uri

To connect to a Distributor instance, the application must be configured with a Distributor Base Uri, which takes the form: `http://<DistributorServerName>:<PortNo>` where:

* `<DistributorServerName>` is the FQDN of the server on which the Distributor service is installed (you can alternatively use the IP address of the server).
* `<PortNo>` is the port on which the Distributor instance is listening for client connections; by default this is 8731.

# Base Uri management within your application

To enable your licensed application to use Distributed resources, you will need a mechanism to maintain the Distributor Base Uri within your system. The main options are:

* Pass the Distributor Base Uri as a command line argument during the application installation process.
* Add an option to configure the Distributor Base Uri to an existing Settings or Configuration dialog in your application. 

If you pass Distributor Base Uri as a command line argument you can call `SpAgent.CommandLineProcessing.ProcessDistributorArgs(args)` in your application `Main()` to have it passed through to a default configuration store if you desire.  

# Base URI Storage approaches

If you wish the application end-user to be able to set (and if necessary update) the Base URI setting then it will need to be stored within an area that the Application is able to write to regardless of which user is active.  Your options are:

* Leverage the pre-existing License Storage Location as already configured (which already has the correct read/write permissions set).
* Leverage a pre-existing application settings storage location that is created under application control.  
		
## Using the License Storage Location

By default `SpAgent.Distributor.Configuration` will maintain the Base URI in the storage configured via the SoftwarePotential.Configuration.(Multi|Single)User Package's ConfigureStorage() implementation. See [http://docs.softwarepotential.com/Configuration.Local.SingleUser-README.html](http://docs.softwarepotential.com/Configuration.Local.SingleUser-README.html) or [http://docs.softwarepotential.com/Configuration.Local.MultiUser-README.html](http://docs.softwarepotential.com/Configuration.Local.MultiUser-README.html) for further information about Sp.Agent's storage mechanisms.  
		
Using the pre-existing license storage has the advantage of removing the need for your application to create the required storage location with the necessary read/write permissions.

With this approach SpAgent will attempt to write/read the Distributor Base URI to/from an app setting in a file named `Sp.Agent.Distributor.dll.config`, with the following format:

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
		<appSettings>
			<add key="Sp.Agent.Distributor.BaseUri" value="http://DISTRIBUTORSERVER:8731/" />
		</appSettings>
	</configuration>		

By default the `Sp.Agent.Distributor.dll.config` file is created within a `Sp.Agent.Distributor.Configuration` subfolder within the pre-existing single or multi-user license storage location; the name/location of this folder can be changed in the `ConfigureStaticDistributorDiscovery()` method in `SpAgent.DistributorCustomizations.cs`.  

## Leveraging a pre-existing settings storage mechanism within your application

This approach makes sense if you already have a separate settings storage system in your application and wish to maintain the Distributor Base Uri in a similar fashion to other application settings.

For example, if you add a setting within your application's configuration system, e.g. a **MyApplicationSettings.DistributorUri** property. One can adjust the the Distributor's configuration to obtain the Base Uri from your property by adjusting `ConfigureStaticDistributorDiscovery()` as follows:

    static partial class SpAgent
	{
		static partial void ConfigureStaticDistributorDiscovery(Action<Func<Uri>> configure)
		{
			configure(() =>
			{
				var result = new Uri(MyApplicationSettings.DistributorBaseUri);
				Debug.WriteLineIf(result == null, @"No Distributor BaseUri is currently Configured. For a Distributor to be used, your Externally Managed Settings will need to yield a non-null BaseUri");
				return result;
			});
		}
	}
	
## Leveraging a pre-existing `app.config` file

If you are not interested in allowing the application end-user to set or update the Distributor Base URI then the simplest approach of all may be to maintain it as a [read-only] setting in the application configuration file (`web.config`, `app.exe.config`, etc). If you use this approach, the responsibility of ensuring a valid Base Uri is set in the `app.config` would typically be owned by the application installer (especially as writing to an `app.config` is typically an elevated action).  

*NB It is important to note that with this approach it will NOT be possible for an end user to update the Distributor Base URI once set*. 
		
For example, if the following app setting is created (with the values of `<DistributorServer>` and `<PortNo>` set appropriately) by the installer at deployment time:

	<configuration>
		<appSettings>
			<add key="Sp.Agent.Distributor.BaseUri" value="http://<DistributorServer>:<PortNo>"/>
		</appSettings>
	</configuration>   	

then one would make the following modification to the `ConfigureStaticDistributorDiscovery()` method to have it source the configured value correctly:

    static partial void ConfigureStaticDistributorDiscovery( Action<Func<Uri>> configure )
	{
		configure( () =>
		{
			var result = SpAgentDistributorConfiguration.FromAppConfig().ReadBaseUriOrDefault();
			Debug.WriteLineIf( result == null, @"No Distributor BaseUri is currently Configured. For a Distributor to be used, the app.config needs an appSetting named Sp.Agent.Distributor.BaseUri" );
			return result;
		} );
	}

# No Local Storage Configured

In the unlikely event that you don't have any local license storage configured (e.g. you are not using a SoftwarePotential.Configuration.(Multi|Single)User Package) then you will need to maintain Distributor Base Uri using code within your application as per one of the following approaches covered earlier in this document:

* Leveraging a pre-existing settings storage mechanism within your application
* Leveraging a pre-existing app.config file
 
In addition, the lack of such local storage precludes:

* Checking out Distributor licenses so that applications can work when Disconnected from Distributor
* Use of any local licenses in parallel with Distributor licenses
  
If you do not have local storage configured but still wish your application to avail of floating/concurrent licenses served from a **continually available** Distributor then you should stub out the Storage aspect of the Agent Context Configuration as follows:

	static partial class SpAgent
	{
		// Stub out any storage for licenses or Distributor config files on the local machine
		// Assume all state will be maintained on a continually available Distributor
		static partial void ConfigureStorage( Action<Func<IAgentCommenceConfigurationPhase, IAgentDistributorsConfigurationPhase>> configure )
		{
			configure( agent => agent
				.DisableStorage() );
		}
	}

# Prompting for Distributor Base Uri

Whichever approach you take to the storage of the Distributor Base Uri, you may wish to prompt the end user to enter the Distributor endpoint Uri if no value has been set e.g. by triggering an Options Dialog.
 
You can do this simply by passing a lambda to `configure()` that returns either:

- a Valid Uri if a Distributor is to be used 
- null if the user has opted not connect to a Distributor

To ensure a good user experience you should first attempt to read the Uri from whatever storage you've configured for your application and subsequently write the value entered by the user to storage. 

The following is a possible skeleton implementation:

    static partial class SpAgent
    {
        static partial void ConfigureStaticDistributorDiscovery(Action<Func<Uri>> configure)
        {
            // Cache the selected endpoint here so multiple calls to the callback can save the value
            var selectedEndpoint = default(Uri);
            // TODO load saved value from config file or similar
            configure(() =>
            {
                // If user has selected one from dialog, or we got one from the 
                // config, we return it directly rather than re-prompting every 
                // time the Distributor Service needs to be contacted for any reason
                if (selectedEndpoint != null)
                    return selectedEndpoint;

                var ok = RunDialog(out selectedEndpoint);
                if (!ok)
                {
                    System.Diagnostics.Trace.WriteLine("User Cancelled Distributor Service Endpoint selection");
                    return null; // a NotLicensedException will be raised by the default behavior
                }

                // TODO save selectedEndpoint into storage so next app startup can pick up the value

                return selectedEndpoint;
            });
        }

        static bool RunDialog(out Uri selectedEndpoint)
        {
            do
            {
                //var dialogResult;
                // TODO prompt user, put result into dialogResult and output into selectedEndpoint
                var distributorConfigForm = new DistributorConfigForm();
                var dialogResult = distributorConfigForm.ShowDialog();

                if (dialogResult == DialogResult.Cancel)
                {
                    selectedEndpoint = null;
                    return false;
                }

                selectedEndpoint = new Uri(distributorConfigForm.EndPoint);

                // Validate the selected Endpoint to give immediate feedback to the user as to 
                // whether the nominated endpoint is a Valid Distributor
            } while (!SpAgent.Distributors.CanConnect(selectedEndpoint));
            return true;
        }
    }


