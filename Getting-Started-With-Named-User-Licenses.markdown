---
layout: github.io
category: pages
title: Getting Started with Named User Licensing
tagline: Using Distributor to manage Named User Licenses for your Application
---

Welcome to the **Getting Started with Named User Licensing** guide. The purpose of this document is to get you, a first time user, up and running quickly when using the Software Potential Service to implement Named User licensing of your .NET application using Distributor.

(Please see [Getting Started With Distributor](http://docs.softwarepotential.com/Getting-Started-With-Distributor.html "Getting Started With Distributor") for more details on how to use Distributor.)

# Overview
Software Potential Distributor allows you to quickly and effectively implement floating / concurrent licensing of your applications.  Distributor supports two types of floating / concurrent licenses:

- **Anonymous User Licenses:**- Distributor licenses that can be accessed by any user running an instance of a licensed application that is Distributor enabled
- **Named User Licenses:** - Distributor licenses that can only be accessed by a user running a Distributor-enabled instance of a licensed application **AND** where the user is assigned as a named user in Distributor.

**NOTE: It is not possible to mix Anonymous User and Named User licenses in the same Distributor instance.**

A Named User license in a Distributor instance can only be accessed by those users who have been assigned in the Distributor server.  A named user license is therefore more restrictive that an Anonymous User license as with the latter there are no restrictions on the users that may access the license.

# How it Works

When issuing a Distributor license a vendor must select the appropriate Named User option to indicate it is a Named User license. The vendor must also set a default minimum duration for a customer assignment

When the Named User license is activated in Distributor users may be assigned by the customer's Distributor administrator without any vendor involvement. A user is assigned via the Distributor web UI by first entering the appropriate username (as a string) and then selecting the Minimum Assignment Duration.

To access the Named User license a Distributor client requesting a feature or a checkout must provide a username with each request to the Distributor service. (For details on how to configure the client application to pass the required username see the Named User Configuration topic in [http://docs.softwarepotential.com/Configuration.Distributor-README.html](http://docs.softwarepotential.com/Configuration.Distributor-README.html "Configuration.Distributor-README").)

In order for the request to be processed the username provided by the client application instance must match a username assigned in Distributor server. If no username is provided or the username provided does not match an assigned username then the runtime returns a **NotLicenseException**.

A user remains assigned until such time as explicitly unassigned by the Distributor administrator. The user cannot be unassigned until such time as the minimum assignment duration has expired.

# Issuing a Named User License
When issuing a Named User Distributor license a vendor must:

- Set the **Named User** checkbox
- Set the number of **Lock Days** i.e., the minimum duration allowed for a user assignment which must be 0 or greater. A value of 0 means user can be reassigned at will by the customer.

![IssueNamedUserLicense](http://i.imgur.com/g0DYQi5.png)

## Maximum Number of Assigned Users
For a given Named User license the total number of users that can be assigned cannot exceed the maximum concurrency limit set on the license e.g. only 5 users may be assigned to a license that allows only 5 concurrent seats.  All users will be assigned to the license for the Minimum Assignment Duration set on the license.

## Issue a License Programmatically
You automate the issuance of a Distributor license using the Software Potential web API at [https://srv.softwarepotential.com/SLMServerWS/LicenseManagementWS.svc](https://srv.softwarepotential.com/SLMServerWS/LicenseManagementWS.svc). To create a Named User Distributor license progammatically you will need to:

- Create a ```LicenseInfo``` object with: 
 - the ```Limitations.MaxConcurrentUsage``` property set to the number of concurrent usages for the license,  
 - the ```IsDistributor``` property set to **True**
 - the ```L:RequiresNamedUsers``` Custom Tag added
- Call `CreateLicense()` with the `LicenseInfo` object you just created. 

# Activating a Named User License
When a Named User license is activated in Distributor the license is shown in the list of licenses for the Product, with the **Requires Named Users** field set to "YES"

![NamedUserLiceneeList](http://i.imgur.com/GCPGbzS.png)

When a Named User license is activated a Named User administration page is also added to the web UI. This page lists the products for which Named Users licenses are installed. The customer selects a product to manage the associated Named Users, specifically to assign and reassign users.

![NamedUserPage](http://i.imgur.com/T1BF6Y5.png)

## Mixing Anonymous User and Named User Licenses in a Distributor Instance
**It is not possible to simultaneously license Anonymous Users and Named Users for the same  Product in a single Distributor instance .** 

Once a Named User license for a product is activated in a Distributor instance then only named users will be able to access any valid Anonymous User and Names licenses in that Distributor instance. In other words the Distributor instance will only respond to client requests that contain valid assigned usernames.

For example, if an Anonymous User license is installed in a Distributor instance with previously activated Named User licenses for same product, the features in the new Anonymous User will be available to client applications requesting feature leases or checkouts for Named Users, but will NOT be available to client applications requesting feature leases or checkouts for Anonymous Users. 

Similarly if a Named User license is mistakenly activated in a Distributor instance with previously activated Anonymous User licenses then the Anonymous User licenses license will immediately become unavailable to client applications submitting requests for Anonymous Users.

In the unlikely event that you wish to license the same Product for both Anonymous Users and Named Users in a single customer site then you will need to deploy separate Distributor instances for each license type.

## Durations of User Assignments in Distributor
Typically a single Named User license is activated in Distributor so that:

- The total number of users that can be assigned cannot exceed the concurrency limit of the license
- The Minimum Assignment Duration set in the license applies to all assigned users

It is possible to activate multiple Named User license for a given product in Distributor. Where multiple Named User licenses exist for the same product the total number of users that may be assigned is the total of the concurrency limits of each license.

If all the licenses have the same Minimum Assignment Duration then this will apply to all assigned users. If the licenses have different Minimum Assignment Durations then different assigned users may have different minimum durations.

For example, consider a situation where the following licenses are activated for a given product:

- License #1: Product A, x5 Seats, 30 Days Duration
- License #2: Product A, x3 Seats, 60 Days Duration.

The total number of users that may be assigned is 8, comprising x5 with 30 Days duration and x3 with 60 Days duration.

Should another license with x5 Seats and 60 Days duration be subsequently activated the  total number of uses that may be assigned changes to 13, of which x5 are 30 Days and x8 with 60 Days duration.


# Managing Named Users Assignments
Users are assigned to a Named User license via the Named User page in the Distributor web UI.

![NamedUserAssignment](http://i.imgur.com/k1Q0Lff.png)

To help with user assignment this page provides an overview of:

- **Remaining Users:** - The number of users that can still be assignment for a given Assignment Duration (as it is possible to have several Named User licenses installed each with different assignment durations)
- **Assigned Users:** - the current user assignments

## Assigning Named Users
A user is assigned by 

- Entering one or more validly-formatted usernames in the User field.
- Selecting an Minimum Assignment Duration from the drop down ("Lock Days" on the portal).

**NOTE:** A user cannot be assigned without a minimum assignment duration being specified. 

To assign multiple users at once enter the list of usernames separated by semicolons.

The following is displayed for each assigned user:

- The username assigned 
- The minimum assignment duration 
- The date after which the user can be unassigned
- The **Unassign** button if it is possible to unassign the user.

![NamedUserAssigned](http://i.imgur.com/lefJj32.png)


### Username Format
A vendor may specify the username format required for a given product. This approach enables a vendor to support the widest possible set of identity providers e.g. Windows username, application generated username, third party identify providers etc.

Distributor does not validate the user names entered to ensure they comply with a vendor's format. So it is important the administrator assigning users is aware of the required usename format to enter.

It is possible to indicate the required username format to the Distributor administrator via the value of the ```Slps.Distributor.Web.NamedUserInstructions``` key in the ```<appSettings>``` of the web.config. The text will be displayed in the Help on the Named User admin page.

The following sample key value

	<configuration>
       <appSettings>
          <add key="Slps.Distributor.Service.BaseUri" value="http://localhost:8731/"/>
          <add key="Slps.Distributor.Web.VendorDisplayName" value=""/>
          <add key="Slps.Distributor.Web.ManualActivationRequestInstructions" value=""/>
          <add key="Slps.Distributor.Web.NamedUserInstructions" value="The username should match the user login and be in the format user@domain.com"/>
	  </appSettings>
	</configuration>
will be displayed as shown below
![NULicenseHelpText](http://i.imgur.com/AAby4zr.png)

## Unassigning Named Users
Once the Minimum Assignment Duration has expired for a given user assignment it is possible to unassign that user, removing access to the product in Distributor for the user.

**NOTES:**
 
- A user remains assigned until such time as explicitly unassigned.
- A user that is unassigned while checked out can check in but won't be able to either acquire  or checkout features again until reassigned by the administrator.
- If a user is unassigned after selecting a checkout but before submitting the checkout request, the request will result in a ``Sp.Agent.Distributor.NoLongerAvailableException``. (This is similar to the situation where a user selects a checkout to acquire but that checkout is taken by another user before the checkout request is submitted.)
