---
layout: github.io
category: pages
title: SoftwarePotential.Configuration.Removable-README
tagline: README for SoftwarePotential.Configurtion.Removable NuGet package from Software Potential
---

Welcome to the SoftwarePotential.Configuration.Removable-&lt;PermutationShortCode&gt; NuGet Package.

IMPORTANT: this package is intended to be used in concert with:

* SoftwarePotential.Licensing-&lt;MyProduct&gt;-&lt;MyVersion&gt; NuGet package (must be installed for the code to compile correctly). See [the associated Licensing README](http://docs.softwarepotential.com/Licensing-README.html) for more information.
* SoftwarePotential.Protection-&lt;PermutationShortCode&gt; NuGet package. See [the associated Protection README](http://docs.softwarepotential.com/Protection-README.html) for more information.

# Key Constraints and Advantages of this Removable Configuration Package

This package is intended to offer a licensing configuration for applications that work with licenses stored on a removable USB device.  The key advantage of this configuration is that it allows one to issue licenses securely locked to a USB device such that the licenses can be accessed by an application when the USB device is inserted into the machine on which the licensed application is running. Once the device is removed the licenses on the device are no longer available to the application.

The removable store supports Perpetual, Time Limited and Subscription licenses.

Multiple licenses can be stored on a single removable USB device, and a machine may have multiple removable USB devices inserted.

# License Storage Location

Adding this NuGet package to your application configures the runtime such that licenses may be stored on supported secure USB devices.

This package can be used in conjunction with any other top level SoftwarePotential.Configuration-<shortcode> package. As a result it is possible to configure a runtime to support any combination of local, network (Distributor) and/or removable license storage.

N.B You will need to add a reference to System.Runtime.Serialization in your project.

# USB SDK

The USB SDK zip file can be obtained by sending an email request to support@softwarepotential.com. 

The SP_USBSDK zip file contains

* The USB Device Manager needed to initialize the USB device.
* The DLLs required to communicate with the USB device.


 
## USD Device DLLs 

Currently support is limited to the Marx CryptoBox.  You need to add a reference to the 32-bit or 64-bit Marx CBIOS4NET.dll file to your project as appropriate.

These DLLs are distributed in the Lib folder of the SP_USBSDK directory.

## USB Device Manager Installation
The files for the USB Device Manger are located in the UsbDeviceManager folder in the SP_USBSDK directory.

It is necessary to install a device driver as part of the USB Device Manager installation. To do this, run with elevated permission the **CBUSetup.exe** located in USbDeviceManger\CBUSetup folder with the following CLI command: ```CBUSetup.exe /CRYPTOKEN /DRIVERONLY```

For driver installation error codes refer to the readme file located in the CBUSetup folder.

# USB Device License Storage Initialisation

It is important to note that a USB device must be initialized before it is distributed to the end customer.

The Software Potential USB Device Manager (UsbDeviceManager.exe) tool is used to initialize the removable license store on the USB device. 

To initialise a device using the USB Device Manager:

1.	Login with valid Software Potential user credentials.
2.	A list of the IDs of all available devices will be displayed. If no device ID is listed, please check that a device is correctly inserted.
3.	Enter the USB device administration password (Supplied by the USB device vendor).
4.	If more than one device is inserted select the CryptoBox to be initialised from the list of available devices. 
5.	Click **Initialize** to initialize the device. (**N.B.** Re-initializing a new store will delete any existing store and associated licenses.)
6.	If successful you will receive the message ```<CryptoBoxId> successfully initialized```.



Once initialized the USB Device is now ready for license activation and can be distributed to an end customer.


# Issuing Removable Licenses

You can issue a license configured for a removable store in the Software Potential portal by selecting the "Removable" option in the Environment and Locking section.

# Troubleshooting

## Activation Exceptions

An attempt to activate a license with either:
 
* no USB device inserted, or
* an uninitialized device 

will result in a ``LinkedLicenseNoStorageActivationServerException``.

An attempt to activate a license when multiple USB devices initialized by the same vendor are inserted will result in a ``LinkedLicenseAmbiguousStorageActivationServerException``

To avoid this this ensure that when activating a license, only the USB device on which you wish the license to be installed is inserted.

If during an activation process an attempt is made to write a license file to a different USB device to the one inserted at the time the activation was requested, or there is no USB device inserted, a ``LicenseTargetEnvironmentMismatchException`` will result.
This is more likely to be encountered in manual activations.



## Runtime Exceptions

An attempt to access the store with either

* no USB device inserted
* a USB device inserted but not initialized
* no valid license in the the store

will result in a ``NotLicensedException``.

If the device is corrupted such that the removable license store is missing but the anchor file (.rds extension) remains, the initial VerifyStoresInitialized() check will fail with a ``WritingStorageInaccessibleException``.  

## Error Messages
The CryptoBoxActivationExceptionHelper class in the SpAgent.Configuration.Removable file provides helpful USB specific error messages for the above Exceptions.