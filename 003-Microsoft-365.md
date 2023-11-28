# Configure SSO and LCM for Microsoft 365

## Overview

This guide provides instructions for integrating Okta with Microsoft
Office 365 so that the following use cases can be demonstrated:

-   Single Sign-on (SSO) from Okta to Microsoft Office 365

-   Lifecycle Management (LCM) for Microsoft Office 365

This guide also provides instructions for setting up a scheduled import
and a scheduled workflow which will exercise the Microsoft Graph API in
your E5 sandbox to prevent it from expiring.

Before you can complete this guide you must have already signed up for a
Microsoft E5 sandbox and configured it with a custom DNS domain. A
custom DNS domain is required because it's not possible to set up
federation against the default onmicrosoft.com domain.

This guide assumes that you have at least one test user (in addition to
your admin user) defined in your Okta org.


### Introduction

Microsoft Office 365 uses Microsoft Azure AD as its user store and to
manage federation for single sign-on. This means that when you're
configuring integration with Microsoft Office 365 in Okta, you're really
configuring integration with Azure AD - even though the integration name
in the OIN "Microsoft Office 365".

Okta's single sign-on to Microsoft Azure AD uses a protocol called
WS-Federation. The WS-Federation standard, and the related WS-Trust
standard, were developed by Microsoft (and a group of other companies)
in the days before SAML 2.0. Today, it's unusual to see WS-Federation
except for in relation to integration with Microsoft.

>Note: Microsoft has recently added support for SAML 2.0 but the
integration is more limited than when using WS-Federation.

The Okta Lifecycle Management integration with Microsoft uses
Microsoft-specific APIs to manage users in Azure AD. Integration with
Microsoft is relatively simple if you only need to manage the basic
profile attributes available via Microsoft public APIs. However, it can
get pretty complex if you need to integrate with an environment that
uses Microsoft Azure AD Connect (AAD Connect) to synchronize an
on-premises AD with Azure AD in the cloud.

This document covers the simplest integration you need to build a demo
system.


---


## Prerequisites

To complete this lab you will need : 

1. Get your Office 365 tenant (see instructions bellow)

2. Have users on your tenant (imported from Active Directory or created manually in Okta) in order to assign the application and validate the user flow.


### Create an Office 365 Dev (Free) Tenant

1. Register for a free Microsoft Dev Account

    1. Open the website https://developer.microsoft.com/en-us/microsoft-365/dev-program


    2. Click on "Join Now"
    ![](images/009/o365setup-001.png)


    3. Create a new account by clicking on "Create One!"
    ![](images/009/o365setup-002.png)


    4. Click on "Get a new email address"
    ![](images/009/o365setup-003.png)


    5. Register for a new email address. Use as name convention `{{idp.name}}`. So your email will be `{{idp.name}}@outlook.com`.

        > **Remember to save the password in a secure place, as you will need it later.**

        ![](images/009/o365setup-004.png)


    6. Complete the registration form by putting your information. We suggest to keep English as language.
       ![](images/009/o365setup-005.png)


    7. Click on "Set up E5 subscription"
      ![](images/009/o365setup-006.png)


    8. Select "Configurable Sandbox"
      ![](images/009/o365setup-007.png)


    9. Complete the Microsoft 365 E5 sandbox form.
        
        Use `admin` as username and `mywiclabNAMESURNAME` as domain.
        
        > **Remember to save the password in a secure place, as you will need it later.**
     

        ![](images/009/o365setup-008.png)


    10. Complete the registration by inserting your phone number and complete the validation challenge using the OTP that you receive by SMS
          ![](images/009/o365setup-009.png)

          >  To resume:
          >  - Your Office 365 Tenant:  mywiclabNAMESURNAME.onmicrosoft.com
          >  - Your Admin Account:      admin@mywiclabNAMESURNAME.onmicrosoft.com
          >  - Your Email Address:      my{{idp.name}}@outlook.com

2. Configure you Microsof 365 Tenant
    1. Open the website https://www.office.com/login?login_hint=admin@mywiclabNAMESURNAME.onmicrosoft.com and login using your password

    2. Create a new domain **`wiclabNAMESURNAME.onmicrosoft.com`**
    
          > **NOTE: the name is the same of the O365 tenant, without the word `my` at the beginning.**

           TODO: COMPLETE - subdomain registration

        > To resume:
        >  - Your Office 365 Tenant: mywiclabNAMESURNAME.onmicrosoft.com
        >  - Your Admin Account:     admin@mywiclabNAMESURNAME.onmicrosoft.com
        >  - Your Custom DNS Domain: wiclabNAMESURNAME.onmicrosoft.com


---


## Add Microsoft Office 365 Application to Okta

Okta maintains a specific integration for Microsoft Office 365 in the Okta Integration Network (OIN). To add this to your Okta org, follow these steps:

1.  If not already opened, launch your Okta Admin console from the dedicated button 

![](images/009/image048.png)


2.  Navigate to **Applications \> Applications**.

3.  Click **Browse App Catalog** to open the Okta Integration Network
![](images/009/image01.png)


4.  Search for and select the **Microsoft Office 365** integration.
![](images/009/image02.png)


5.  Click **Add integration**.
![](images/009/image03.png)



6.  Complete the *Microsoft Tenant Name* field. 
    > This must match the tenant name you chose when setting up your Microsoft tenant. If
    > your Microsoft tenant is **`mywiclabNAMESURNAME.onmicrosoft.com`** then
    > your Microsoft tenant name is **`mywiclabNAMESURNAME`**.

7.  Click **Next**.
![](images/009/image04.png)



8.  Select the radio button for **WS-Federation**.

![](images/009/image05.png)

9.  Enter the *username* and *password* for the administrator of your
     Microsoft tenant.
> Your Microsoft Tenant Admin has this syntax **`admin@mywiclabNAMESURNAME.onmicrosoft.com`**.

![](images/009/image06.png)


11. Click **Fetch and Select** next to Office 365 Domains.


12. Select the custom DNS domain

    > **IMPORTANT:** you have to choose the domain that we added in the previous step. If you use the default one (the one that is created automaticaly with the tenant) it will not work!
    This is because default domain cannot be federated, due to a limitation imposed by Microsoft.
    If you followed the name convention, your domain should be **`wiclabNAMESURNAME.onmicrosoft.com`**

    ![](images/009/image07.png)


13. Click **Select**.


14. Set *Application username format* to **Custom**.


15. Enter the following for the expression 

    **`String.substringBefore(user.login,\"@\") + \"@wiclabNAMESURNAME.onmicrosoft.com\"`**

  ![](images/009/image08.png)


16. Click **Done**.

Your Okta org and your Microsoft 365 tenant are now configured for
single sign-on from Okta to Azure AD.

> Note: You can't test single sign-on until you have enabled provisioning
> and assigned at least one user to this application in Okta.


---


## Configure provisioning

The single sign-on configuration performed in the previous section
allows your Okta org to assert identity information to your Microsoft
365 tenant and it allows your Microsoft 365 tenant to trust it. However
that's not enough for single sign-on to complete. Each identity asserted
by Okta must link to an existing account in Azure AD.

The required Azure AD accounts could be created manually, or synced from
a common identity source such as an on-premises AD but, in this case,
you will set up provisioning in Okta so that it can automatically manage
accounts in Azure AD. It will create accounts when users are assigned to
the Office 365 application and deactivate these accounts when users are
unassigned. It will also manage user attributes and licenses.


### Enable integration

Accounts in Azure AD can be managed via Microsoft APIs. To enable
provisioning, Okta must be granted authority to call these APIs. Follow
the steps below to grant this access and enable integration:

1.  If you are not already there, open your Okta administration UI, navigate to **Applications \> Applications**, and select the **Microsoft Office 365** application definition.

    ![](images/009/image09.png)


2.  Click on the **Provisioning** tab and click the **Configure API

    ![](images/009/image010.png)


3.  Check the checkbox for **Enable API integration**.


4.  Click the **Authenticate with Microsoft Office 365** button.


5.  Authenticate as the administrative user for your Microsoft tenant.

    ![](images/009/image011.png)


6.  Click **Accept** to allow your Okta org to manage users in your O365 tenant

    ![](images/009/image012.png)

7.  Click **Save**.


The API connection for provisioning is now in place. Provisioning
configuration is now shown under the Provisioning tab. The integration
configuration you just created is under the Integration tab.


### Enable provisioning to app

In this section you will enable provisioning to Microsoft 365. This will
include creation of accounts in Azure AD when users are assigned to the
application, updating attributes in Azure AD when things change in Okta,
and deactivating accounts in Azure AD when users are unassigned from the
application in Okta.

Note: In general, Okta does not delete accounts from applications - it
only deactivates them. This ensures that Okta is not responsible for any
data loss in the backend system that could be triggered by deleting an
account.

Follow these steps to enable provisioning:

1.  Click **Edit** to set the options for Okta provisioning to Azure AD.
    ![](images/009/image015.png)


2.  Click the **Enable** checkbox for *Create Users*.


3.  Click the **Enable** checkbox for *Update User Attributes*.


4.  Click the **Enable** checkbox for *Deactivate Users*.

    ![](images/009/image014.png)


5.  Click **Save**.



### Configure attribute mapping

When a user is assigned to an application, attribute mapping determines
the default values that will be set for attributes in the application.
If attribute mapping is not configured, values for attributes must be
manually set whenever a user is assigned.

For Microsoft 365, the licenses and roles to be assigned to users can be
set up in attribute mapping. This is what you will configure here.
Follow these steps:



1.  If not already there, open the **To App** settings under the **Provisioning** tab.
    ![](images/009/image015.png)


2.  Scroll to the bottom of the page and click **Show Unmapped
    ![](images/009/image016.png)


3.  Click the **Edit** icon for the *Licenses* attribute.


4.  Select **Same value for all users** from the drop-down list.\
    ![](images/009/image017.png)


    > The available options are shown.
    
    ![](images/009/image018.png)



5.  Select the checkboxes for the following licenses:

    -   Sharepoint Online (Plan 2)
    -   Office Online
    -   Office 365 ProPlus
    -   Exchange Online (Plan 2)
    -   Exchange Online Advanced Threat Protection
    -   Microsoft Teams


6.  Scroll to the bottom of the window and click on **Save**.
![](images/009/image020.png)


Provisioning is enabled and attribute mapping is done. You're ready to test.


---


## Test provisioning and single sign-on

Now that provisioning and single sign-on have both been configured, you
can test them by assigning your Okta admin user to the Microsoft Office
365 application and then attempting single sign-on.

This section uses the Okta admin user ***your.name*\@your company domain.com** as the
test user. This user will be mapped to
***your.name*\@yourdemodomain.com** by the custom username mapping you
set up when configuring single sign-on.


### Assign user to Microsoft Office 365 application

For this test you will directly assign your Okta admin user to the
Office 365 application. You'll notice that it's also possible to assign
via group membership - you'll set that up later.

1.  If not already there, navigate to **Applications \> Microsoft Office 365**


2.  Select the **Assignments** tab.

    ![](images/009/image021.png)


3.  Click the **Assign** button and select **Assign to People** from the **Assignments** tab


4.  Select your admin user and click **Assign**.

    ![](images/009/image022.png)

    >
    > The User Name has been set based on the custom expression you
    > specified during configuration of single sign-on.
    >
    > The *Immutable ID* is blank. This would only be populated if the user
    > was AD sourced and already had an immutable ID synced from Azure AD
    > via Microsoft AD Connect or similar. In this case, the immutable ID
    > will be populated during provisioning to Azure AD.
    >
    >![](images/009/image023.png)
    >
    > Licenses have been set based on the attribute mapping you created.
    >

5.  Scroll down to the *roles* and select the **Global Administrator**
    ![](images/009/image024.png)


6.  Scroll to the bottom and click **Save and Go Back**.
    ![](images/009/image025.png)


7.  Click **Done**.
    ![](images/009/image026.png)

    >
    > In the background, Okta is now attempting to create this user in your
    > Microsoft 365 tenant and assign the selected roles licenses. This
    > should only take a few seconds to complete.
    >

    ![](images/009/image027.png)


8.  In the Okta Admin UI, navigate to **Reports \> System Log**.

    ![](images/009/image028.png)


9.  Confirm that the records indicating successful provisioning are found.



### Test single sign-on (SSO)

You can now test single sign-on to Microsoft 365 for the assigned user.

1.  Open a new browser window that is not signed into Okta or Microsoft

2.  Navigate to your Okta tenant. e.g. **https://{{idp.name}}.okta.com**

3.  Authenticate as your test user: e.g. **your.name\@yourcompanydomain.com**



4.  Click the tile for **Microsoft Office 365 Office Portal**.

![](images/009/image029.png)



5.  Click **Yes** to stay signed in.
![](images/009/image030.png)



6.  Click the identity icon in the top-right of the page.

7.  Click **Sign out** to clear the session. Close the browser you used
![](images/009/image031.png)


---


## Configure group assignment

In the previous section, you assigned a user directly to the Microsoft
365 application. It was useful to do this so that you could assign the
Global Administrator role. However, for assigning users in bulk, it's
more usual to assign a group to an application and then assign users to
the group.

When assigning a group to an application, you can specify values for
application attributes. These will override any mapping for those
attributes in the application definition. If you don't specify any value
for an attribute, the mapping in the application definition will be
applied instead.



### Create a group

You will now create a group that will be assigned to the Microsoft
Office 365 application.

1.  In the administration UI for your Okta org, navigate to **Directory \> Groups**.
![](images/009/image032.png)
2.  Click the **Add group** button.



3.  Enter **O365Users** as the Name of the group.


4.  Enter **Office 365 Users** as the Description.

5.  Click **Save**.
![](images/009/image033.png)



### Assign group to application

You can assign a group to an application by either assigning the group
within the application definition or by adding the application to the
group definition. In this case you will add the application from the
group definition.

1.  If not already there, navigate to **Directory \> Groups**.

![](images/009/image034.png)

2.  Click on the link for the **O365Users** group.


3.  Select the **Applications** tab in the group properties.

4.  Click **Assign applications**.
    ![](images/009/image035.png)


5.  Click the **Assign** button next to the *Microsoft Office 365*
    ![](images/009/image036.png)


    and select the checkboxes for the following licenses:

    -   Sharepoint Online (Plan 2)
    -   Office Online
    -   Office 365 ProPlus
    -   Exchange Online (Plan 2)
    -   Exchange Online Advanced Threat Protection
    -   Microsoft Teams



6.  Scroll to the bottom of the page and click **Save and Go Back**.
    ![](images/009/image038.png)


7.  Click **Done**.
    ![](images/009/image039.png)



### Assign user to group

You will now assign a test user to the *O365Users* group. This will
cause the user to be assigned the Microsoft Office 365 application
which, in turn, will trigger provisioning of an account.

*Be aware that Okta can automatically assign people to a group based on an attribute or group membership.*

1.  If not already there, navigate to **Directory \> Groups**.
and select the Okta group called *O365Users* (different from the one that is AD sourced)


2.  Select the **People** tab.
    ![](images/009/image040.png)


3.  Click **Assign people**.


4.  Click the **+ icon** for a test user in your Okta org.\
    ![](images/009/image041.png)

5.  Click the **Done** button to save the configuration.


The user is now assigned to the group and will be assigned to the
Microsoft Office 365 application using the attribute mapping associated
with the group assignment.
![](images/009/image042.png)


---


## Test single sign-on

You can now test single sign-on to Microsoft 365 for your test user.

1.  Open a new browser window that is not signed into Okta or Microsoft

2.  Navigate to your Okta tenant.

3.  Authenticate as your test user: e.g.
    > ***youruser@yourcompanydomain.com***

4.  Click the tile for **Microsoft Office 365 Word Online**.
  ![](images/009/image043.png)



5.  Click **Yes** to stay signed in.
    ![](images/009/image044.png)


6.  Click the identity icon in the top-right of the page.

7.  Click **Sign out** to clear the session. Close the browser you used
    ![](images/009/image045.png)



---



Congratulations! You have successfully configured Lifecycle Management
and Single Sign-On to Microsoft Office 365. Your demo environment can
now be used to demonstrate these capabilities to customers.

