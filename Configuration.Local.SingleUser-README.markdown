---
layout: github.io
category: pages
title: SoftwarePotential.Configuration.Local.SingleUser-README
tagline: README for SoftwarePotential.Configuration.Local.SingleUser NuGet package from Software Potential
---

Welcome to the SoftwarePotential.Configuration.Local.SingleUser-&lt;PermutationShortCode&gt; NuGet Package.

IMPORTANT: this package is intended to be used in concert with:

* SoftwarePotential.Licensing-&lt;MyProduct&gt;-&lt;MyVersion&gt; NuGet package (must be installed for the code to compile correctly). See [the associated Licensing README](http://docs.softwarepotential.com/Licensing-README.html) for more information.
* SoftwarePotential.Protection-&lt;PermutationShortCode&gt; NuGet package. See [the associated Protection README](http://docs.softwarepotential.com/Protection-README.html) for more information.

# Key Constraints and Advantages of this SINGLE USER Configuration Package

This package is intended to offer a licensing configuration for applications that manage licenses completely within the current user's Profile. This means your application and it's licenses **can only be used by a single Windows user on each target machine**. The key advantage of this is that this does not force one to write and distribute a separate installer, i.e. an MSI package or similar. (See [the MultiUser equivalent](http://docs.softwarepotential.com/Configuration.Local.MultiUser-README.html) if this is inappropriate for your application).

# License Storage Location Base Path

Adding this NuGet package to your application configures licenses to be stored in the Application Data folder within the Windows user's Profile directory. i.e. out of the box, the directory used by the code in this package results in a base path such as ``C:\Users\MyUser\AppData\Local\``

# Customizing the license store path

Out of the box, the path used is derived from your Company and Product names as supplied to the Software Potential service. This is only intended as a placeholder and you are free to customize the exact names used.

Within the Base Path, this package generates a subdirectory structure for your applications licenses as follows: ``MyCompany\MyProduct\MyVersion`` (Having a subdirectory structure like this is necessary as this area of the users profile directory is shared among all applications on the machine.)

To change either the base path, or the subdirectory used for your application's license storage area, please refer to the `SpAgent.ConfigureLocalStorePath()` method (in `SpAgent.Configuration.Local.Customizations.cs`).

# Troubleshooting

The key requirement of this configuration is that your application be running with sufficient privilege to 
- access the base directory (e.g. ``C:\Users\USERNAME\AppData\Local\``
- create folders within the base directory (e.g. ``MyCompany\MyProduct\MyVersion``
- write files within the above area

Any failures will result in an Exception derived from ``StorageInaccessibleException``.