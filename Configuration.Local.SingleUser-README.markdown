---
layout: github.io
category: pages
title: SoftwarePotential.Configuration.Local.SingleUser-README
tagline: README for SoftwarePotential.Configuration.Local.SingleUser NuGet package from Software Potential
---

Welcome to the SoftwarePotential.Configuration.Local.SingleUser-&lt;PermutationShortCode&gt; NuGet Package.

IMPORTANT: this package is intended to be used in concert with:

* SoftwarePotential.Licensing-&lt;MyProduct&gt;-&lt;MyVersion&gt; NuGet package (must be installed for the code to compile correctly)
* SoftwarePotential.Protection-&lt;PermutationShortCode&gt; NuGet package

The code in this README is intended to provide a terse introduction to some key techniques related to configuring Software Potential-licensed applications, which can be used by a single Windows user on each target machine.  

It is NOT intended as a full product walkthrough or as a sample of a real application. Please refer to:

- <https://github.com/SoftwarePotential/samples/> for example code
- <http://support.inishtech.com/> for the forum and other support materials

License store overview
----------------------------------
In this scenario, the licenses are typically stored in an application data folder belonging to the Windows user, e.g. ``C:\Users\MyUser\AppData\Local\MyCompany\MyProduct\MyVersion``

CONFIGURATION
==============

Customizing license store path
-------------------------------
To change the license store path, you need to modify `SpAgent.ConfigureLocalStorePath()` method (in `SpAgent.Configuration.Local.Customizations.cs`).
