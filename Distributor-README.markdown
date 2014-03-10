---
layout: github.io
category: pages
title: Sp.Distributor-README
tagline: README for Sp.Distributor NuGet package from Software Potential
---

Welcome to the Sp.Distributor-&lt;PermutationShortCode&gt; NuGet Package.


# Purpose of this Distributor Package

This package contains the Distributor Server components required to support floating/concurrent licensing models in your license application. If your application is intended to work with floating/concurrent licenses then one or more Distributor instances must be installed on your customer's premises.

When Distributor is used, the floating/concurrent licenses are activated on a Distributor instance and licensed applications that wish to avail of these licenses must be configured to connect to the Distributor instance. Please see [Getting Started - Licensing with Distributor](http://docs.softwarepotential.com/Getting-Started-With-Distributor.html) for more details on how to use Distributor to implement floating/concurrent licensing models in your application.

An application configured to work with Distributor can use both Local and Distributor licenses in parallel. SpAgent will attempt to utilize any available local licenses before attempting to request resources from the Distributor instance.

# Packaging Distributor
In addition to enabling your application via the Distributor client components (see [SoftwarePotential.Configuration-README](http://docs.softwarepotential.com/Configuration.Distributor-README.html) for more details), you will also need to package the Distributor Server so that it can be installed on-premise by your customers. This process is straightforward and involves:

- Installing the Sp.Distributor-&lt;PermutationShortCode&gt; NuGet package to obtain your permuted edition of the Distributor server components;
- Packaging the Distributor Server components for installation by your end customer;
- Customizing Distributor settings as required.

## Installing the Sp.Distributor NuGet package
To obtain the components required to install and run a Distributor instance on your customer’s premises you will need to first install the **Sp.Distributor-&lt;PermutationShortCode&gt;** NuGet package in your Visual Studio Solution. 

Once installed, you will find the `Sp.Distributor-<PermutationShortCode>` package contents in the `packages`  folder in your solution directory. The components required to run a Distributor instance on a customer’s premises can be found in the `Slps.Distributor.Host` subfolder. 

## Installing Distributor Server
The Distributor components are designed to fit into an appropriate installation/deployment process that makes sense in the context of your application or system. For example, to install a Distributor server you might wish to create an installer using an installer product of your choice or alternatively simply include a step in a custom installation script which would XCOPY the required binaries to the target machine. 

Typically the Distributor server components are packaged separately from your application, requiring separate installers for your protected application and your Distributor server components.  

Whatever installation approach you take you need to ensure in your installation process that you:
  
- copy the `Slps.Distributor.Host` folder to an installation directory on the machine intended to host the Distributor Server.
- call (elevated) the ``Install.cmd`` batch file in the ``Slps.Distributor.Hosts`` folder with the **-Install** option e.g. from the command line ```C:\<InstallationDirectory>\Slps.Distributor.Hosts\install.cmd -install```.

Our [Distributor installer sample](https://github.com/SoftwarePotential/samples/tree/master/Licensing/Desktop/Distributed/Installer) contains a WIX-based installer project. You just need to install your **SoftwarePotential.Distributor-&lt;PermutationShortCode&gt;** NuGet package and build the solution to generate a fully-functional Distributor installer.

# Customizing Distributor Server Settings
You have the option to alter some or all of the default Distributor server settings to suit your deployment requirements.
## Distributor Endpoint
By default the Distributor service listens on Port 8731 for feature allocation requests from appropriately configured licensed applications. A new port can be allocated to suit a customer environment by changing the port number value of the ```Slps.Distributor.Service.BaseUri``` key in ```..\Slps.Distributor.Hosts\Services\Slps.Distributor.Services.dll.config``` 

### Distributor Base Uri
To connect to a Distributor instance, the application must be configured with a Distributor Base Uri, which takes the form: `http://<DistributorServerName>:<PortNo>` where:

* `<DistributorServerName>` is the FQDN of the server on which the Distributor service is installed (you can alternatively use the IP address of the server).
* `<PortNo>` is the port on which the Distributor instance is listening for client connections; by default this is 8731.

## Distributor Web Admin Portal
Distributor is administered via a web portal available on Port 2468 by default. A new port can be allocated to suit a customer environment by changing the value of the Port property in the ```Slps.Distributor.Host``` element in ```..\Slps.Distributor.Host\Slps.Distributor.Host.exe.config``` 

## Distributor UI Customizations
You may also wish to change some of the Distributor default UI settings using keys in the ```<appSettings>``` section of the `web.config` file as follows:

- **Vendor Name:** All Distributor web administration pages by default display the vendor name of your Software Potential account. If you wish to display an alternative name you can set this alternative in the ```Slps.Distributor.Web.VendorDisplayName``` key.
- **Activation Instructions:** During the manual activation process the Distributor page displays some default instructions to be followed by the user when handling activation request files etc. You can enter alternative instruction text to be displayed using the ```Slps.Distributor.Web.ManualActivationRequestInstructions``` key

# Managing Distributor Service
Once installed the Distributor Service can be stopped/started/restarted using the [Services Snap-in](http://technet.microsoft.com/en-us/library/cc736564.aspx) (find the **Software Potential Distributor Service** and right click to select **Stop**, **Start** or **Restart** as appropriate).

Distributor can also be managed via install.cmd e.g. ``Install.cmd -stop``, ``Install.cmd -start``, ``Install.cmd -install```, ``Install.cmd -uninstall``

