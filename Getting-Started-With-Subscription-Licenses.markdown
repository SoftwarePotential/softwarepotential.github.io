---
layout: github.io
category: pages
title: Getting Started With Subscription Licenses
tagline: Getting Started With Subscription Licenses
---

#  What is a Subscription License?
A Subscription License is used to effect a licensing scheme whereby the customer is maintaining a regular payment schedule and the software should mirror this by extending the right to use the software until the end of the currently paid period (which is calculated relative to the commencement date of the contract).

During a given period, the activation of the license yields time-limited access to the software. At the end of that period, the license becomes inactive until a renewal (retrieval of an updated license pertaining to the new period) is completed.

Consider a 1 Month Subscription License issued on 01 Jan 201X: at the end of each monthly period the license is renewed until the end of the next period:

![Subscription License Timeline](http://i.imgur.com/8nIz7Kr.png)

The key point to note is that, irrespective of when the license is activated/renewed, the Expiration Date is calculated relative to the Issue Date and NOT the Activation Date. 

## Subscription vs Time-Limited Licenses

Unlike a Subscription License, a Time-Limited license will expire after a predefined period calculated relative to the Activation Date, not the Issue Date. Additionally, a Time-Limited license is not renewable and would need to be reissued in order to effect any change to the Expiration Date.

# Issuing a Subscription License
To issue a Subscription License, select the **Licenses** tab on the main menu and then **Issue New License** option in the left-hand navigation bar.  In the **Lifecycle Type** section select **Subscription** option to set the required Subscription Period and Grace Period.

![Issue Subscription License](http://i.imgur.com/B2Er8rU.png)

## Subscription Period
The period of a Software Potential subscription license is denominated in calendar Months (entering 1 year 6 months translates to an 18 calendar month cycle).

## Grace Days

At the end of each period, the software will normally cease to function immediately until the license is successful renewed.

In order to smoothly handle delays or other issues with renewal processing, one should strongly consider applying a number of **Grace Days** which allows the software to remain functional pending the completion of renewal. Note that the expiration date still continues to be computed relative to the start of the contract (i.e. the customer does not gain any advantage as a result of a renewal being 'delayed').

## Auto-renewal Option
By default Subscription Licenses are set to **Auto-renew**. This means all renewal requests against the Software Potential service will be automatically approved without any intervention by the Licensor. If you wish to explicitly manage the approval of renewal requests then one can disable auto-renewal by unchecking the Auto-Renew checkbox.

# Activating a Subscription License
When a Subscription license is activated, its **Expiration Date** is set as an offset to the License **Issue Date**, the date on which the license is created. On activation the Expiration Date is set such that the license is valid until the end of the current period, irrespective of when the activation takes place.

The following is an example of how the Expiry Date is set for a 1 Month Subscription License, depending on when the license is activated:

![Subscription License Activation](http://i.imgur.com/z8MKfY4.png)

The key point is that the Expiry Date is always calculated from the Issue Date, not the Activation Date.

# Issuing a Subscription License Programmatically
To issue a Subscription License programmatically using the Software Potential web service, in addition to the usual license properties required you will need to set the the following  `LicenseInfo` properties specific to subscription licenses:

- IsRenewable
- Subscription Period
- Grace Days
- Autorenewal (Optional)
- Start Date (Optional).

## IsRenewable
The `LicensInfo.IsRenewable` property must be set for a subscription license

## Subscription Period
Add the `L:SubscriptionPeriod` custom tag to `LicenseInfo.CustomTags` and set its value to the required subscription period in calendar months e.g. for an annual subscription period set the value to 12.

## Grace Days
Set the `LicenseInfo.Limitations.GracePeriod` to the required number of grace days allowed. 

## Renewal
The `L:SubscriptionRenewUntil` Custom Tag controls renewals of the subscription license. The subscription license can be renewed up to and including the date value set.

Subscription licenses are issued with **Auto-renew** property set by default and you do this programmatically by not adding the `L:SubscriptionRenewUntil` tag to the license. To disable **Autorenew** on issue the `L:SubscriptionRenewUntil` tag must be added and its value set to the date of issue. 

Please see the later section on Controlling Renewals for details on how to control ongoing renewals of the license once activated using the web service API calls.

##StartDate
By default the StartDate will default to the issue date.  However if you wish you can set a specific period start date using `LiceneInfo.Limitations.StartDate` e.g. to synchronize the subscription period of several subscription licenses issued on different dates. 


# Renewals
An attempt can be made to renew a Subscription License at any stage; the renewal request can be submitted:

 * Prior to the expiration of the period
 * During the Grace Period (After the expiration, but while the license is still valid during the Grace Days after the Expiration Date) 
 * After the Grace Period has elapsed (at which point licensed code would no longer be functional).

A renewal request made after the Expiration Date of the license will extend the license for another period. However, a renewal request made prior to the Expiry Date of the license will not advance the Expiration Date i.e., it is not possible to extend the duration of a subscription license prior to completion of the current period. 

The following table shows the effect of different renewals attempts on the Expiration Date of a 1 Month Subscription License:

![Subscription License Renewal](http://i.imgur.com/AfgTNpT.png)

## Controlling Renewals
Typically a licensor wishes to be in position to approve/control attempted renewals of subscription licenses,  to ensure that commercial conditions associated with the renewal have been met e.g. that the required subscription payment has been made by the customer.

Subscription licenses are Auto-renewable by default, i.e., any attempt to renew the license will be automatically approved without any intervention.

![Subscription License Autorenewal](http://i.imgur.com/GRGpa0b.png)

If one wishes to control renewals of a given license (e.g. approve it each renewal based on receipt of a corresponding subscription payment) then one can turn off Auto-renewal for that license. This means the license issuer must explicitly approve each renewal of the given license either: 

- manually using the Software Potential license management UI or
- programmatically via the License Management web service.

### Using Software Potential UI
When one views the details of a Subscription License that has Auto-renewal turned off, the date until which renewals are authorized is shown as the **Renewal Authorized Until** date. This date initially defaults to one period unit from the license Issue Date.

Any renewal request made prior to this date will be fulfilled; any attempted renewal after this date will be refused.

![Subscription License Renew Until](http://i.imgur.com/vWOFUv7.png)
 
To approve renewals for the selected license. one can adjust the **Renewal Authorized Until** date by the required number of periods. Clicking the **"+"** / **"-"** buttons increments/decrements the date by one subscription period. 

In our earlier example extending the **Renewal Authorized Until** to July 23 ensures this license can be renewed for the next period once the current period expires.

![Subscription License Renew Until Increment](http://i.imgur.com/vMtXExb.png)

Once can re-enable Auto-renewal at any point by checking the Auto-renew checkbox while viewing the license details.

### Using Software Potential Webservice API
It is also possible to manage the following aspects of Subscription License Renewals using the License Management webservice API :
- Disable Auto-renewal
- Re-enable Auto-renewal
- Increment the **Renewals Authorized Until** date
- Set a specific **Renewals Authorized Until** date.

To disable Auto-renewal for a given license one calls **DisableAutoRenewal()** with the LicenseId of the license to be disabled:
    
    public static void DisableSubscriptionLicenseAutoRenewal( License license, UserCredentials Credentials)
    {
    	using (var client = CreateLicenseManagementClient(credentials))
     	{
           client.DisableAutoRenewal(license.LicenseId);
           Console.WriteLine("License {0} is disabled for autorenew", license.ActivationKey);
    	}
    }

To enable Auto-renewal call **EnableAutoRenewal()**:
    
    public static void EnableSubscriptionLicenseAutoRenewal(License license, UserCredentials credentials)
    {
    	using (var client = CreateLicenseManagementClient(credentials))
     	{
           client.EnableAutoRenewal(license.LicenseId);
           Console.WriteLine("License {0} is enabled for autorenew", license.ActivationKey);
    	}
    }


To authorize renewals for one or more future periods (i.e., increment the licence's existing **Renewal Authorized Until** date) one calls **AuthorizeRenewal()** with the LicenseId of the license to be approved and the number of periods required:
    
	public static void AuthorizeSubscriptionLicenseRenewal(License license, int numberOfPeriods, UserCredentials credentials)
	{
		using (var client = CreateLicenseManagementClient(credentials))
		{
			client.AuthorizeRenewal(license.LicenseId, numberOfPeriods);
			Console.WriteLine("License {0} is authorized for renewal for {1} License Periods", license.ActivationKey, numberOfPeriods);
		}
	}

Finally one can set an arbitrary **Renewals Authorised Until** date by calling **SetSubscriptionRenewUntil()** with the LicenseId and the desired DateTime:

Note that the supplied date only controls the last date at which a renewal will be permitted; the resulting Expiration date is computed in the normal manner - the end date of the period during which a given activation takes place.
    
	public static void SetSubscriptionLicenseRenewUntil(License license, DateTime renewUntil, UserCredentials credentials)
	{
		using (var client = CreateLicenseManagementClient(credentials))
		{
			client.SetSubscriptionRenewUntil(License.LicenseId, renewUntil);
			Console.WriteLine("License {0} RenewUntil is set to {1}", license.ActivationKey, renewUntil);
		}
	} 