# Integrate Active Directory

# Overview

Many companies, especially small- and medium-sized enterprises, use Microsoft Active Directory (AD) as their primary on-premises directory.  They typically manage their user’s account information, including identity attributes; passwords; and group memberships, in Active Directory.

When these companies look to adopt a new identity platform, such as Okta, they naturally want to be assured that it will integrate with AD so their users and existing management processes are not impacted.  They may also want to understand how they can later migrate away from Active Directory as the primary source of identity information.

This guide provides step-by-step instructions for integrating an Active Directory into your Okta demonstration environment.  You will be able to show Delegated Authentication, Just-In-Time (JIT) provisioning from AD to Okta, and provisioning from AD to Okta via scheduled import.

### Agent architecture

![](images/002/ad-agent-infographic.png)


---


# Add Active Directory


## Initiate the Virtual Infrastructure

In this workshop, you will be provided with a Virtual Infrastructure including a Windows Virtual Machine (VM). To initiate it, complete the following steps:

1. In the right column of the Lab website, under Virtual Desktop, Click **Launch**.
    This will open the Virtual Infrastructure tab in your browser.

    ![](images/002/image42.png)


2. Click **Launch Virtual Infrastructure**.
This will initiate the VM built which may take a few minutes to complete. You can see the progress in the "VM is starting" screen and when the VM is ready, you will see the desktop of your Windows Server. The Windows Server includes a pre-installed Active Directory (AD) and browsers (Chrome and Firefox) necessary to proceed with the lab.

   > **Note:** Please make sure to complete all subsequent  steps of the hands-on in this virtual environment, unless otherwise stated.


---


## Initiate integration and download agent installer

Active Directory is integrated with Okta as a Directory Integration. 

Launch the lab AD environment in your browser. The Windows server will have Google Chrome and Firefox pre-installed and available on the Desktop. Launch a new browser window and follow the instructions below to navigate to your Okta Org and download the AD agent installer. 


1. Navigate to **Directory > Directory Integrations** in the Okta admin console
   
    ![](images/002/image1.png)


2. Click **Add Active Directory**.

    ![](images/002/image2.png)


3. Click **Set Up Active Directory**

    ![](images/002/image3.png)


4. Click the **Download Agent** button.
The AD Agent installer is downloaded to your local machine.

    You need to get this installer onto your Windows Server instance.  There are a couple of ways you can do this:

    * Copy the downloaded file to the clipboard and then paste it onto the desktop of the Windows Server.  RDP will take care of the file transfer.
    * Copy the link shown after local download is complete and use it with a browser on the Windows Server instance to download the agent. Authentication is not required.

    ![](images/002/image4.png)

    * Log in to your Okta tenant using a browser on the Windows Server instance and use steps 1-4 above to download the installer.

    > The AD Agent can also be found, along with a lot of other downloadable assets, under Settings > Downloads in the Okta admin console.


---


## Install agent on Windows Server

You will now install the AD Agent onto your Windows Server. In this demo environment, this Windows Server is a domain controller.  In a real environment, the AD Agent can be installed on any Windows Server that is a member of the Active Directory domain.



1. Launch the AD Agent installer by double-clicking the executable. It should have a filename of the form: **OktaADAgentSetup-x.xx.x-xxx-xxxxxxx.exe**.

    ![](images/002/image5.png)


2. Click **Next**.

    ![](images/002/image6.png)


3. Click **Install**.

    The install starts.

    If you don’t have the Microsoft Edge WebView package installed, it will be downloaded and installed.  Then the agent will be installed.

    When the installation is complete, you will be prompted for configuration information:

    ![](images/002/image7.png)


4. Click **Next** to accept the offered AD Domain.
You only have one domain in this demonstration environment.

    ![](images/002/image8.png)


5. Click **Next** to Create or use the OktaService account (recommended).
   
    ![](images/002/image9.png)


6. Enter and confirm a password for the *OktaService* account.
This password can be whatever you like.  Since this is a demo environment, maybe just make it the same as your administrator so you don’t have to remember another password.


7. Click **Next**.


8. Click **Next** on the Proxy Configuration page.

    ![](images/002/image10.png)


9. Enter the URL of your Okta org: **`https://{{idp.name}}.okta.com`**

    The installer is going to make a connection to this URL so that you can authenticate and authorize the AD Agent for your Okta org.


10. Click **Next**.

    At this point the installer launches an embedded browser.  In v3.14 and above, this is always an Edge Web View process - regardless of your default browser settings.

    ![](images/002/image11.png)


11. Authenticate to your Okta org as an administrator.
    
    You may need to complete MFA as part of the login process.

    >In general, it is strongly recommended that you do not use a personal admin account to set up agents.  If this account is later removed, the agent could also lose access.  For a demo system you can take the risk with the benefit that it’s easier and quicker this way.

    >If you do create a dedicated admin account to register AD agents, it will need the Organizational Admin and Application Admin roles.

    After login completes, you will see this consent prompt:

    ![](images/002/image12.png)


12. Click **Allow Access** to grant the AD Agent permissions in your Okta org.

    The browser window closes and the installer completes registration of the AD Agent.


13. Click **Finish** to exit the installer.

    The AD Agent is now running on your Windows Server and will make an outgoing connection to your Okta Org.  You will now see it within your Okta Admin console.


---


## Complete integration configuration

The AD Agent running on your Windows Server is now registered with your Okta org.  You can now complete integration configuration back in your Okta admin console.


1. Navigate to **Directory > Directory Integrations** in the Okta admin console.

    You should see that an Active Directory integration has been registered but is showing as *Not yet configured*:


    ![](images/002/image13.png)


2.  Click on the **Active Directory** link of the new integration.

    ![](images/002/image14.png)

    > On the _Basic Settings_ page you can determine which parts of Active Directory will be connected to your Okta tenant.  Only users and groups in the specified containers will be synced to Okta when provisioning is enabled. By default, the entire Active Directory is selected.


3. For users, uncheck the checkbox at the **dc=oktaice,dc=com** level.


4. Check the checkboxes for the OUs containing your demo users.

    ![](images/002/image15.png)


5. For groups, uncheck the checkbox at the **dc=oktaice,dc=com** level.


6. Check the checkboxes for the OUs containing your demo groups.

7. At the bottom of the page you have the option to set the Okta username format.  This determines which attribute is matched against the username entered on the Okta login page when performing Delegated Authentication.

    Select **Email address**

    ![](images/002/image16.png)



8. Click **Next**.

    > The agent is configured with the configuration you have provided.  When complete, a confirmation is shown:

    ![](images/002/image17.png)


9. Click **Next**.

    > The *Build User Profile* page is loaded.  This loads the attribute schema from your Active Directory so you can specify which attributes should be synced into Okta.

    ![](images/002/image18.png)

    > The suggested attribute list is sufficient but you can add additional attributes if you like.  This is important if you have defined custom attributes in Active Directory.

10. Click **Next**.
    
    ![](images/002/image19.png)


11. Click **Done**.

    The AD integration settings are shown:

    ![](images/002/image20.png)

    > It’s interesting to note that this page looks a lot like an Application settings page.  The Provisioning, Import, and Assignments tabs have similar functions.

    > You’ll notice a warning that One or more required attributes are not mapped. You can ignore it for now; it’s related to provisioning to AD and you’re going to be using AD as a source and provisioning to Okta.



# Validate integration


## Check agent status

Your Active Directory is now integrated with your Okta org.  The Active Directory agent is running and is maintaining a connection to Okta.  You can see the status of the agent in the Admin console:


1. If not already there, navigate to **Directory > Directory Integrations** in the Okta Admin console and select the **Active Directory** integration.

    ![](images/002/image21.png)


2. Select the **Agents** tab.


3. Note the status of the agent.

    > Each time Okta loses connectivity with the agent, and each time connectivity is restored, all admins in your Okta org will get an email notification.  If you want to prevent this email, you can deactivate the agent on this page.  You can also turn off notifications under **Settings > Account**.


---


# Import from Active Directory 

In this section you will configure provisioning from AD to Okta.  This will allow AD to act as a source for user and group information.  Once provisioning is enabled, user and group information can be pulled from AD by performing an import.  Imports can be scheduled or run on demand.



---


## Perform an import

You will now perform an import.  When an import is started, Okta instructs the AD Agent to pull user and group information from AD and return it to Okta.

The user information is used to update previously imported users and to queue new users for matching and import (either manual or automatic depending on configuration).

The group information is used to create AD-sourced group definitions in Okta and update group membership for imported users.



1. If not already there, navigate to **Directory > Directory Integrations** in the Okta Admin console and select the **Active Directory** integration.
    
    ![](images/002/image27.png)


2. Select the **Import** tab.


3. Click **Import Now**.

    You are given the choice of performing an incremental or full import.  An incremental import will only process changes made in AD since the last import.  This is the first import so all users and groups will be processed either way.

    ![](images/002/image28.png)


    4. Click **Import**.

    The import is performed. A progress bar is shown on the screen.  When the import is complete, a summary is shown:


    ![](images/002/image29.png)

    Imported groups are automatically created as AD-sourced groups in Okta and can be found under **Directory > Groups**.

    Based on the default provisioning configuration, imported users are not automatically created in Okta. Instead they are made available for manual review.


5. Click **OK**.

    The imported users are shown along with a suggested action.

    If an imported user is matched (or partially matched) to a user in Okta, the suggested action will be to assign the AD user to the Okta user. Otherwise, as is the case here, the suggested action is to create a new Okta user.


    ![](images/002/image30.png)


6. Check the checkbox that corresponds to one of your AD users (e.g.**Emily Boone**).

    ![](images/002/image31.png)


7. Click **Confirm Assignments**.

    ![](images/002/image32.png)


8. Check the checkbox to **Auto-activate users after confirmation**.
This will activate the new user account so that it can be used.


9. Click **Confirm**.

    A new Okta user is created based on the imported AD user.  The imported AD account is assigned to the new Okta user.  This assignment would allow Delegated Authentication to work even if the account names didn’t match.


---


## Check imported groups

You will now check that the imported groups have been created as groups in Okta.


1. In the Okta admin console, navigate to **Directory > Groups**.

    ![](images/002/image33.png)


    In the list of groups, you should see the groups from your Active Directory listed.  Notice that these groups are tagged with the Microsoft logo so you know they are AD-sourced groups.  The description of each group shows its location in AD.


2. Click the link for one of the AD-sourced groups (e.g.**US West**).

    ![](images/002/image34.png)


    The *Assign people* button is inactive.  It’s important to note that an AD-sourced group is managed by Active Directory; you can’t add users to it from within Okta.


    If you want to be able to manage group memberships of an AD group you should look into the *Push Group* functionality.  This allows the membership of an Okta group to be pushed down into a group in Active Directory.


---


## Check imported user

You will now view the user that you imported from Active Directory in Okta.

1. In the Okta admin console, navigate to **Directory > People**.

    ![](images/002/image35.png)


2. Click on the link for the user you just imported.

    ![](images/002/image36.png)


3. Click **Profile**.

    You can see that many user attributes have been synced from Active Directory.  This is controlled by the attribute mapping configuration under provisioning in the Directory Integration definition.

4. Scroll down to the *Additional Active Directory Attributes* section.

    ![](images/002/image37.png)


5. Click **Show Attribute Names**.
    This will allow you to see the Okta attribute names as well as the display names.

    Here you can see Directory-specific AD attributes that have been synced to Okta.  These can be referenced from Expressions to control things like application attribute mapping or group rules.

    ![](images/002/image38.png)

    These attributes are controlled by the active_directory profile for your directory under Directory > Profile Sources.


---


## Test user login with Delegated Authentication

Now that you have a user in Okta who has an assigned account in Active Directory, that use should be able to authenticate to Okta using their Active Directory credentials.



1. Start a new browser session.  This could be a private browsing window or a different browser process.

2. Navigate to your Okta homepage: **https://{{idp.name}}.okta.com**.

    ![](images/002/image39.png)


3. Enter the Email of the user that you imported to Okta. \
    e.g. **emily.boone@oktaice.local**


4. Click **Next**.

    ![](images/002/image40.png)


5. Enter the Active Directory password for your AD user.

    > Note: Password for all Active Directory users is   **`Tra!nme4321`**


6. Click **Verify**.

    At this point, Okta requests that the AD Agent validate the provided credentials (username and password) against Active Directory.  The result is returned to Okta. Assuming the validation is successful, first-time login proceeds as normal.

7. Complete first time login.  The steps required will depend on the policies configured in your org.  It’s likely you’ll need to register an additional factor.

    When login is complete, you will the user launchpad:

    ![](images/002/image41.png)

    Your imported user has authenticated to Okta using their Active Directory credentials.


---


**Congratulations!** You have successfully imported groups, and a user, from Active Directory.  You could now assign the user to applications just like any other Okta-sourced user.  Methods to assign to an application include:

* Assign the application directly to the AD-sourced user.
* Assign the application to an AD-sourced group that the user is a member of.
* Manually add the AD-sourced user to an Okta group that is assigned to the application.
* Create a group rule (based on user attributes or group membership) which adds the AD-sourced user to an Okta group that is assigned to the application.
* Assign AD, as a *Directory*, to an Okta Group that is assigned to the application.



