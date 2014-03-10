---
layout: github.io
category: pages
title: Getting Started with Distributor
tagline: Using Distributor to manage Concurrent/Floating Licensing for your Application
---
Welcome to the **Quick Guide to Distributed Licensing with Software Potential**. The purpose of this document is to get you, a first time user, up and running quickly when using the Software Potential Service to implement floating/concurrent licensing of your .NET application.

**NB: The Sp.Distributor NuGet package is available from v3.2.1950 so you will need to update your permutation if it is earlier than this.** 

# Overview
Software Potential Distributor allows you to quickly and effectively implement floating / concurrent licensing of your applications.  One or more Distributor instances can be quickly installed on the customers’ premises enabling:

- Rapid deployment of a large installed base of a licensed application where it is not practical to install licenses on each installed instance (Enterprise Licensing)
- Multiple installed instances of a licensed application where the number of installed instances exceeds the number of available licensed seats for the application
- Web farm scenarios where the number of instances of the software can vary over time with performance and load factors (Dynamic Licensing)
- Virtualized environments where secure license storage is not achievable e.g. ability to clone and/or rollback VMs etc (Virtual Licensing).

Distributor has been generally available from the 3.1.1921 release of the Software Potential Service.  Distributor clients can interoperate with server instances of different versions from that release onward.

# How it Works
When an application is Distributor-aware, licenses can be activated and stored in a central Distributor service and then dynamically allocated across multiple deployed instances of the application. Each application instance includes a local` Sp.Agent` extension that must be configured to identify a Distributor Service instance. A service instance can be hosted locally on one of the client machines or, more typically, on a separate network-accessible machine.

Whenever protected code that requires a license is encountered for the first time, the `Sp.Agent` component will query the Distributor service for the Feature required to execute the protected code. The service will yield a lease for the requested feature if there is one available (based on  the licenses installed in Distributor instance and allocations held by other competing application instances.)
 
Any such allocated leases are automatically renewed until such time as the application instance is closed (enabling execution not to be subject to a network round-trip every time).

## Checkout / Disconnected Mode
With Distributor, it is also possible to switch a protected application to Disconnected mode. This facility is useful where the application needs to run in a context where it will not be possible to communicate to a Distributor service instance for a period of time e.g. when the user is working remotely while out of the office or on vacation.

To operate in Disconnected mode the user must first, while connected to Distributor, *check out* the required Features for a given period of time. These checked out Features are then reserved on the service for this extended period and cached on the local machine for the duration of the checkout period, allowing the Protected application to run without any contact with the service.

The cached features can be checked in (at a time when network connectivity has been restored) under the control of the application. At this point, the long term leases on the server are released and allocation once again switches to Connected mode.

# Enabling Your Application to work with Distributor
To configure your application for use with Distributor you will need to add the **SoftwarePotential.Distributor.Configuration-&lt;PermutationShortCode&gt;** NuGet package to your solution and provide a user facility to configure the application with a Distributor Endpoint URI. 

To connect to a Distributor instance, the application must be configured with a Distributor endpoint URI, which takes the form: `http://<DistributorServerName>:<PortNo>` where:

* `<DistributorServerName>` is the FQDN of the server on which the Distributor service is installed (you can alternatively use the IP address of the server).
* `<PortNo>` is the port on which the Distributor instance is listening for client connections; by default this is 8731.

Please see the [Configuration.Distributor-README](http://docs.softwarepotential.com/Configuration.Distributor-README.html) for more detailed information on enabling your application to work with Distributor.

# Packaging Distributor Server
In addition to enabling your application via the Distributor client components, you will also need to package the Distributor Server so that it can be installed on-premise by your customers. The process is straightforward and involves:

- Installing Sp.Distributor NuGet package to obtain your permuted edition of the Distributor server components
- Packaging the Distributor Server components for installation by your end customer 
- Customizing Distributor settings as required.

Please see [Sp.Distributor-README](http://docs.softwarepotential.com/Sp.Distributor-README.html) for more details on the packaging and installing of Distributor.

# Installing Distributor Server

Once installed the Distributor Service can be stopped/started/restarted via the [Services Snap-in](http://technet.microsoft.com/en-us/library/cc736564.aspx) (find the **Software Potential Distributor Service**, and right click to select **Stop**, **Start** or **Restart** as appropriate). It can also be managed using install.cmd batch file e.g. ``Install.cmd -stop``, ``Install.cmd -start``, ``install.cmd -install``, ``install.cmd -uninstall``

## Configuring Distributor Server
### Distributor Endpoint
By default the Distributor service listens on Port 8731 for feature allocation requests from appropriately configured licensed applications. A new port can be allocated to suit a customer environment by changing the port number value of the ```Slps.Distributor.Service.BaseUri``` key in ```..\Slps.Distributor.Hosts\Services\Slps.Distributor.Services.dll.config``` 

## Administrating Distributor Server
Distributor is administered via Web Administration portal available at `http://<DistributorServer>:2468/web` where <DistributorServer> is the fully qualified domain name (or the IP address) of the server on which Distributor is installed. An alternative to the default Port 2468 can be configured to suit a customer environment by changing the value of the Port property in the ```Slps.Distributor.Host``` element in ```..\Slps.Distributor.Host\Slps.Distributor.Host.exe.config```.

The admin portal allows your customer to:

- Activate Distributor licenses (in either online or disconnected mode)
- View all licenses installed in the Distributor server
- View current feature usage by connected licensed applications. 

# Issuing a Distributor License
Licenses suitable for Distributor are created using the Software Potential service in the same manner as any other license. The key differences with a Distributor license are:

- The license is identified as **For use with Distributor only**.
- The **Max Instances Per Client** value is set to the number of concurrent usages for the license being created i.e. a value of 2 here will allow two people to use your application at once within the Distributor system. 

Distributor licenses can be created either:

- Manually on an ad-hoc basis via the **Issue New License** page or
- Programmatically via the **CreateLicense** method of the web service API.

## Issuing a License Manually
To issue a Distributor license manually via the Software Potential portal:

- Select **Manage Licenses** tab, then  **Issue New License** option
- Select the **Product** to be licensed from the drop down list  
- Add **Features** (if required)
- In license limitations section, set the **Max Instances Per Client** value to the number of concurrent usages for the license being created i.e. a value of 2 here will allow two people to use your application simultaneously. 

![MaxInstances](http://i.imgur.com/aU46r3V.png)

- Check the **For use with Distributor only** option

![IsDistributable](http://i.imgur.com/ZSLKFQQ.png)

- Click **Issue License**. This will generate an Activation Key that your customer will need to subsequently activate the license in Distributor.

## Issuing a License Programmatically
You automate the issuance of a Distributor license using the Software Potential web API at [http:\\https://srv.softwarepotential.com/SLMServerWS/LicenseManagementWS.svc](https://srv.softwarepotential.com/SLMServerWS/LicenseManagementWS.svc). To create a Distributor license progammatically you will need to:

- Create a ```LicenseInfo``` object with: 
 - the ```Limitations.MaxConcurrentUsage``` property set to the number of concurrent usages for the license,  
 - the ```IsDistributor``` property set to **True**.
- Call `CreateLicense()` with the `LicenseInfo` object you just created. 

Alternatively, to avoid having to create the LicenseInfo object from scratch, you could first create a SKU for the Distributor license and issue your licenses progammatically based on that SKU:

- Create a SKU for a Distributor license and record the SKUId
- Call `GetSKUByID()` with the SKUId (to retrieve the required LicenseInfo object corresponding to the SKU)
- Call `CreateLicense()` with the returned LicenseInfo object. 

In either case the Software Potential service will return you the Activation Key for the license you've just created and you will need to forward this to your customer to enable them to activate the license in a Distributor instance.

# Activating a Distributor License
Distributor licenses are activated using the Distributor Web Administration portal. By default this is located at `http://<DistributorServer>:2468/web` where <DistributorServer> is the fully qualified domain name (or the IP address) of the server on which Distributor is installed. (**NOTE** It is possible to configure a Distributor server instance to use a different port to the default 2468. See *Customizing Distributor Settings*).
 
There are two ways to activate a Distributor license depending on whether the machine on which Distributor is installed has internet access or not.

## Online Activation
If the machine on which Distributor is installed has internet access then:

- Select the **Activation Tab** on Web Administration page
- Enter the Activation Key and click **Submit**. 

![Distributor_ActivationOnline](http://i.imgur.com/kb1dB23.png)

The page will display an appropriate message if the activation has been successful and an appropriate error message if unsuccessful.

![Distributor_ActvationSuccess](http://i.imgur.com/CiqBjRi.png)

## Manual Activation
If the machine on which Distributor is installed has no direct internet access available then:

- Select the **Activation** tab and click on the **Manual Activation** link.

![Distributor_ActivationManual](http://i.imgur.com/OpBlq6C.png)

- Enter the License Key and click **Submit**.
- Copy the content of License Request Data field and send it to the application vendor (e.g. via email, HTTP or FTP) to be processed.
- On receipt of the license file copy it to the file system on machine on which Distributor is installed 
- Navigate to the **Distributor Manual Activation** page, **Browse** to find the license file saved from the previous step and click **Install**.

As with Online activation mode the page will display an appropriate message if the activation has been successful and an appropriate error message if unsuccessful.

## Viewing Activated Licenses in Distributor
Select the **Products** tab and then **Show Licenses** link to display a summary of the license installed. Select the **More** link to see the details of a given license.

![Distributor_ViewLicenses](http://i.imgur.com/zB8QwAe.png)

That’s it! Distributor is now ready to automatically allocate features to instances of protected applications that have been correctly configured to communicate with it.

