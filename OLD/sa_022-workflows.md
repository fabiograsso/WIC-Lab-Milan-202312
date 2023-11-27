## Assign Group Memberships Temporarily Based on Time

**Before you begin** we need to add the Okta Connection.

1.  In the Okta Admin panel, go to Applications -\> Applications and
   search for "**Workflows**".

2.  Open the "**Okta Workflows OAuth**" app and go to the Sign On tab.

3.  Copy and save the **Client ID** and **Client Secret**.

4.  In the Admin Panel go to Workflow -\> Workflows Console.

5.  Open up the Connections tab and click on **+New Connection**.

6.  Search for Okta, click on it and insert the required details: \
   ![](images/Workflows_2/image7-25.png)

8.  Lastly click **Create**

Now that we have the **Connection to our Okta instance** we can add
the **template**.

1.  At the top click on Templates and search for "**Assign Group
    Memberships Temporarily Based on Time**"

2.  Click on it and then on **Add Template** and again **Add Template**\
    ![](images/Workflows_2/image2-27.png) ![](images/Workflows_2/image8-28.png)

3.  Open up the **Determine if user added to temporary group** and the
    **SUB - Remove User from Group** flows and make sure you **choose
    your Okta Connection** and Save the workflows.\
   ![](images/Workflows_2/image3-30.png)

4.  Create a group in Okta called "**Temp Group**"

5.  Go back to your flows and go to the **Tables** tab and open the
    **Temporary Groups** table\
   ![](images/Workflows_2/image5-31.png)

6.  Add the **group name** and the **duration in minutes** and click
    **Create**. The duration will reflect how long a user should stay
    in that group. For testing purposes we'll put in **3
    minutes**.\
  ![](images/Workflows_2/image9-33.png)

7.  Now go back to your **Flows**, **turn ON** the **App
   Event** and **Helper** flows and open up the
    <ins>Schedule</ins> one.\
   ![](images/Workflows_2/image6-35.png)

8.  Once you\'re in, click on the clock icon on the bottom right hand
    corner of the **Scheduled Flow** event. Now schedule the flow to
    run every **5 minutes** (lowest it can go). **Save** it and turn
    the flow **ON**.\
   ![](images/Workflows_2/image1-37.png)    ![](images/Workflows_2/image4-39.png)

9.  Once all 3 flows are on, go back to the **Okta Admin Dashboard** and
    assign a user (create one if needed) to the **Temp Group**.

10. Now to check the workflow on how it ran, open up the **Determine if
    user added to temporary group** flow and go to **Flow History.**

11. You can also check the **Users Added to Temporary Groups** table to
    see the user that has been added.

Time to wait 5 minutes. **Coffee Break!**

Once the 5 minutes are up, open up the **Scan users for removal**
followed by the **SUB - Remove User from Group** and check the Flow
history to see the flow in action.

Also, you can re-check the **Users Added to Temporary Groups** table to
see that the user was removed.



## Send email notifications when Super Administrator Privileges are granted

**Before you begin** we need to add you to the Workflows app and add the
Email Connection (Gmail or Office 365 Mail).

1.  In the Okta Admin panel, go to Applications -\> Applications and
    search for "**Workflows**" and make sure you **are assigned to both
    applications**.

    ![](images/Workflows_1/image30-25.png)

3.  In the Admin Panel go to Workflow -\> Workflows Console.

4.  Open up the Connections tab and click on **+New Connection**.

5.  Search for **Gmail or Office 365 Mail**, click on it and
    then again on **Create**.

6.  A window will open for you to sign in with your Email Account\
    \
    **Note:** You can use your personal or company email. This is a
    connection directly to your email account to be used by Okta
    Workflows to take actions on its behalf, but we're only using it to
    send emails. (You can delete the connection when you're done)\
     ![](images/Workflows_1/image13-27.png)

7.  Once you log in and give it access, you're done!

**The Task! - Create an email alert for when a user has been assigned
the Super Admin role.**

1.  In the Workflows Console, click on the plus "+" next to
    **Folders**.\
 ![](images/Workflows_1/image3-29.png)

2.  Give your folder a name, we went with "**Super Admin Alert**".

3.  Go into your folder and click **+New Flow**\
 ![](images/Workflows_1/image23-31.png)

4.  Once the new flow Opens, click Save, give the flow a name (we went
    with **SuperAdminEvent**). Also, check the box for "**Save all data
    that passes through the Flow?**", this is to remember the flow
    history for debugging purposes.

5.  Click **Save**\
 ![](images/Workflows_1/image27-33.png)


Now it's time to add an event trigger, and for this we'll use an **API
Endpoint**.

1.  In your new flow, click **Add event**\
![](images/Workflows_1/image29-35.png)

2.  Now click on **API Endpoint**, choose Expose as Webhook, copy the
    Invoke URL and close the pop-up.\
 ![](images/Workflows_1/image12-37.png)
    \
    \
    \
    \
    \
    \
    \
    **NOTE:** If you want to find the endpoint again, you **\</\>**
    symbol at the bottom right corner of the **API Endpoint** card.\
 ![](images/Workflows_1/image18-39.png)
    

Now that we have the **API Endpoint**, we'll need to create an **event
hook** that for each time a user or a group receives Super Admin
permissions it will send the information to our API Endpoint.

1.  In the Okta Admin Panel, go to Workflow -\> Event Hooks and click on
    **Create Event Hook**.

2.  Give it a **Name**, paste in the **API Endpoint** where it says URL.
    Notice that since it's a workflow API URL, we don't need to verify
    the endpoint ownership.

3.  Under Subscribe to events pick "**Group's admin privilege granted**"
    and "**A User's admin privileges changed**".

4.  Click Save & Continue\
 ![](images/Workflows_1/image25-41.png)
    

We can take a look at the event hook information that will be sent by
clicking **Preview** under Actions and selecting the event type.

 ![](images/Workflows_1/image22-43.png)

After you select the event type you'll see the output in a JSON format.
This is how the information will be sent to our flow.

There is a lot of information in that JSON and we'll need to filter out
what we need:

 ![](images/Workflows_1/Screenshot1.png)

In order to get the information we need we'll have to create the path to
the attributes:

E.g.

> For **privilegeGranted** it will be:
> **data.events.0.privilegeGranted**
>
> For the target **type** it will be: **data.events.0.target.0.type**

**\
NOTE:** When you go from object to object, you use "." like data.events.
But in a list "\[" you have to specify the position of the object in the
list. For us it will be 0 (zero) since there is only 1 object in the
list (lists start at zero). If we had 2 objects, we'd need to do
**data.events.0.privilegeGranted** and
**data.events.1.privilegeGranted** to get both privilegeGranted objects.

Now that we've set our **Event Hook** to send the information to our
**API Endpoint** and we have our attributes, we'll proceed with
finishing our flow.

1.  Back in our flow, click on "Add function" and select from the
    **Object** category **Get Multiple**.\
   ![](images/Workflows_1/image14-45.png)   ![](images/Workflows_1/image1-47.png)

2.  Now drag the **body** from the API Endpoint card to the Get Multiple
    card\
![](images/Workflows_1/image28-49.png)

3.  In the Get Multiple card, click on the bottom of the card to add our
    attribute paths(list below).\
![](images/Workflows_1/image10-51.png)
    \
    **NOTE:** Once you paste a path press Enter to confirm it.

-   **data.events.0.privilegeGranted -\> What privilege was granted**

-   **data.events.0.actor.alternateId -\> Who gave that privilege**

-   **data.events.0.target.0.displayName -\> The display name of the
  target (who got the privileges)**

-   **data.events.0.target.0.type -\> The target type (user or group)**

-   **data.events.0.target.0.alternateId -\> The target's alternate id
  (groups don't have one)**

You can always rename a field that you have put in place and you can add
a description for it as well.\
![](images/Workflows_1/image11-53.png) ![](images/Workflows_1/image21-55.png) ![](images/Workflows_1/image15-57.png)

**Once you're finished adding them it should look like this:**

![](images/Workflows_1/image2-59.png)

To make sure we know what's what, we\'ll assign a different name for the
value in each path.

1.  Click Add function again and from the **Flow Control** category pick
    **Assign**

2.  Click inside and type a name you can work with (image below). Then
    drag **from the Get Multiple card to the Assign card** the
    corresponding paths.

![](images/Workflows_1/image4-61.png)

![](images/Workflows_1/image24-63.png) ![](images/Workflows_1/image26-65.png)

Now we'll add a condition to only continue if the privilege is **equal
to** Super administrator.

1.  Click Add function, and from the **Branching** category choose
    **Continue If.**

2.  Drag the **Privilege Granted** from the Assign card to the **value
    a** of the Continue If card.

3.  For comparison select **equal to**

4.  For **value b** type in **Super administrator**

5.  For the otherwise message, type **Not a Super administrator** (since
    we only want the super admin privilege).

![](images/Workflows_1/image8-67.png)

What's left is to compose a message, set the Distribution List of emails
that should receive this message and send it. But we'd like to have a
dynamic message based on if the target is a group or a user. So let's
see how we do that:

1.  Click on Add function and from the **Branching** category choose
    **Lookup**

2.  In the **Value** section drag and drop the **Type** field from the
**Assign card**.

3.  Next we'll set the **Value and Result.**

4.  When Value is **User** then the result is **user**

5.  When Value is **UserGroup** then the result is **group**

6.  This time, the **Otherwise** option can be left blank since it can
    only be the above 2 options

**\
![](images/Workflows_1/image17-69.png)

Next we'll compose the body of our email:

1.  Click on Add function and from the **Text** category select
    **Compose**

2.  Now the message will be: **Super administrator privileges have been
    granted to the () by .**

3.  And in between the text we'll drag and drop the required attributes:

![](images/Workflows_1/image19-71.png)

![](images/Workflows_1/image20-73.png)

Now for the mailing list:

1.  Click on Add function and from the **List** category select
    **Construct**

2.  Click inside the card and add the addresses you'd like to send the
    email to:

![](images/Workflows_1/image7-75.png)

Now for sending our email:

1.  Click **Add app action** -\> **Gmail** -\> **Send Email**

2.  Pick the connection and Save

3.  Add a Subject to the Email\
    E.g. **DoNotReply - Super administrator privileges have been
    granted!**

4.  Drag the constructed mailing list in the **To** field

5.  Drag the composed body to the **Body** field

![](images/Workflows_1/image5-77.png)

**Save your flow and turn it on!**

![](images/Workflows_1/image6-79.png)

**To test:**

1.  Go to the **Okta Admin Panel -\> Directory -\> Groups**

2.  Click on **+Add Group**

3.  Create a group called **SuperAdmins**

4.  Go to **Security -\> Administrators**

5.  Click on **Admins -\> Add Administrator**

6.  Type in the name of the group in the **Select admin** box

7.  Under **Role** select **Super Administrator** and click
    **Save Changes**

![](images/Workflows_1/image16-81.png)

That's it, you should have gotten an **email!**

**If you want to see the flow in action, you can go back to your flow
and on the top left hand corner click Flow History.**

![](images/Workflows_1/image9-83.png)
