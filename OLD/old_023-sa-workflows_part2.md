**Before you begin** we need to add the Okta Connection.

1.  In the Okta Admin panel, go to Applications -\> Applications and
   search for "**Workflows**".

2.  Open the "**Okta Workflows OAuth**" app and go to the Sign On tab.

3.  Copy and save the **Client ID** and **Client Secret**.

4.  In the Admin Panel go to Workflow -\> Workflows Console.

5.  Open up the Connections tab and click on **+New Connection**.

6.  Search for Okta, click on it and insert the required details:

![](images/Workflows_2/image7-25.png)

7.  Lastly click **Create**

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
    minutes**.
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
