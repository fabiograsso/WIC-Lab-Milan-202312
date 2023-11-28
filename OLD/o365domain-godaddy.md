

4. Click **Add domain**.

    ![](images/009/o365setup-image14.png)



5. Enter your *Domain name*: **`wiclabNAMESURNAME.onmicrosoft.com`**

    Click **Use this domain**.

    ![](images/009/o365setup-image31.png)

    > *NOTE* Following instruction are an example if you decide to use a real DNS domain. For this lab you can use an *onmicrosoft.com* domain that not require additional steps!

    1. Microsoft looks up the hosting service of the DNS domain you have provided.  If it is hosted by a supported provider (e.g. GoDaddy), automatic verification and configuration is offered:

    ![](images/009/o365setup-image46.png)

    2. Click Verify to start the verification process.
        A pop up window is opened to your hosting provider.

    3. Authenticate to your hosting provider (e.g. GoDaddy).
    An authorization confirmation request is shown:
        ![](images/009/o365setup-image2.png)

    4. Click Connect to allow Microsoft to access your DNS domain at GoDaddy for the purposes of domain ownership verification.
        The connection is used to obtain proof of ownership for your DNS domain.  When complete, the pop up window closes and you are returned to the Microsoft setup wizard:
        ![](images/009/o365setup-image30.png)
        On this page you can select how you will add the required DNS entries to your DNS provider.  The default option for GoDaddy domains is to have this done automatically.

    5. Click Continue.
        ![](images/009/o365setup-image22.png)

    6. Click Add DNS Records.
        A new browser pop-up is opened for GoDaddy.  You might need to authenticate again.  Once authenticated, you will see another authorization request:
        ![](images/009/o365setup-image25.png)

    7. Click Connect to allow Microsoft 365 to add required DNS entries to your DNS domain.

        Setup is performed.  This may take a few seconds.  When complete, the GoDaddy pop-up closes and the M365 Add Domain wizard reports success:
        ![](images/009/o365setup-image8.png)

    8. Click Done.
    The Domains page shows that your custom domain is the default domain for the M365 tenant and is Healthy.



### Make onmicrosoft.com domain the default

When your custom domain is created, it is automatically set as the default domain.  This is a problem because the default domain cannot be configured for Single Sign-On.  You must now configure the onmicrosoft.com domain as the default domain.

![](images/009/o365setup-image10.png)

1. Select the **`mywiclabNAMESURNAME.onmicrosoft.com`** domain.

2. Click Set as default.
 ![](images/009/o365setup-image39.png)

3. Click **Set as default** in the confirmation dialog.
    The **`mywiclabNAMESURNAME.onmicrosoft.com`** domain is now shown as the default:
    ![](images/009/o365setup-image7.png)

Your initial Microsoft 365 tenant configuration is now complete.  You are ready to configure integration with Okta for Single Sign-On and Provisioning.


