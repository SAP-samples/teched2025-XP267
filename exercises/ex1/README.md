# Exercise 1 - Access SAP Cloud ALM and trigger tigger simple commands to your App via SAP Automation Pilot 

In this exercise, we will: 
- access SAP Cloud ALM and explore the metrics visible in Health Monitoring
- interact with App endpoints via SAP Automation Pilot
- setup the integration between SAP Cloud ALM and SAP Automation Pilot

For a better understanding of the currnet use case, please consult the diagram shared below: 
<img src="./images/ex-01-scenario.png" width="700" height="400">

## Exercise 1.1 Access SAP Cloud ALM and explore the metrics visible in Health Monitoring

**Access SAP Cloud ALM** by following this link here: https://xp267-calm-1hdji9xc.eu10-004.alm.cloud.sap/

**Login** with your user 
Select the **Operations** menu item. 
![](./images/01-01.png)

Click on Health Monitoring capability. 
![](./images/01-02.png)

You will land at Health Monitoring Overview screen. 
Make sure Scope has been selected to your subaccount XP267-0XX_CF (e.g. XP267_000_CF)
![](./images/01-03.png)

Click on the tile and you shall be able to see the services/ system visible in the Health Monitoring. 
![](./images/01-04.png)

Click on the service to land to the SAP BTP Cloud Foundry environment Metrics Overview. 
These are all metrics ingested by your CAP app via the Open Telemtry. 
![](./images/01-05.png)

Feel free to click on **All Metrics** tab to explore these metrics further. 
![](./images/01-06.png)

Well done! You can now undestand better the health status of you CAP app. 
It is time to move your SAP Automation Pilot tenant and BTP Cockpit.


## Exercise 1.2 Interact with App endpoints via SAP Automation Pilot

### Exercise 1.2.1 Explore main sections in SAP Automation Pilot

To check out you SAP Automation Pilot tenant you need to go back to you SAP BTP subaccount. 

Your **SAP BTP subaccount is XP267-0XX** (use your hands-on session user e.g. XP266-001) and then click on **Services** dropdown from the lefside menu --> **Instances and Subscriptions** 

From the **Subscriptions** sections --> **Automation Pilot** (see the screenshot below)
![](./images/01-1-01.png)

You shall land SAP Automation Pilot **dashboard page**. 
![](./images/01-1-02.png)

Click on **Provided Catalogs** menu and check out all provided ready-to-use automation commands grouped in variois catalogs. These are 300+ ready-to-use commmands at your disposal. 
![](./images/01-1-03.png)

From the same menu you also can explore: 
- **My Catalogs** - a newly added commands at your disposal
- **Commands** - listing all your commands in a list
- **Inpits** - all available inpputs that can be referenced to any exisitng command

### Exercise 1.2.2 Build our first command in SAP Automation Pilot
Let's build our first command in SAP Automation Pilot. 

Navigate to My Catalogs -> click on **Commands** from catalog named **XP267 Ex01 - Kick Start Commands**
![](./images/1-2-2-01.png)

You will land here - thera are already few commands create and we'll explore these in a bit. For now, click on the button **Create** :
![](./images/1-2-2-02.png)

- **Catalog** - pick up the `XP267 Ex01 - Kick Start Commands`

- **Name** - simpleHttpRequest

Click on **Create** button. 
![](./images/1-2-2-03.png)

Congrats - you had created a command in SAP Automation Pilot. However, as you see, thera are no inputs keys, outputs keys , neither executors for this command. Let's add some! 
![](./images/1-2-2-04.png)

Let's stat with the Inputs. 
Click on **Add** button within the Inputs Key section. 
![](./images/1-2-2-05.png)

Fill in the **Add Input Key** popup: 

- **Name** - `url`

- **Type** - `string`

- Make it `Required` by switching the toggle
  
Click on **Add** button to save it. 
![](./images/1-2-2-06.png)

Now let's add another input since we want to interact with our CAP app , we'll need to define the method of our simple call to this app. 
Click on **Add** button within the Inputs Key section and fill in the **Add Input Key** popup: 

- **Name** - `method`

- **Type** - `string`

- **Default Value Source** - `Static` (use the drop-down)

- **Value** - `GET`

![](./images/1-2-2-07.png)
  
Click on **Add** button to save it. 

Now it is time to add an executor. To do so, click on **Add** button within the **Executors section**.. 
![](./images/1-2-2-08.png)

Within the Add **Executor pop-up**: 

- **Select** where exactly within the automation flow the executor to be placed by click on **Here** button

- **Alias** - `getAppEndpoint`

- **Command** - `HttpRequest` (use an existing command to do http requests)

- **Automap parameters** - leave it `enabled` (by default it is enabled so do not change it)

- Click on **Add** button
![](./images/1-2-2-09.png)

As you see - the executor is now in place and there is an automated mapping between the input key `method` and the parameter `method` within the command. 
![](./images/1-2-2-10.png)

Now let's add few outputs which are to be consumed once the command it is fired and executed. 

To do so, click on Click on **Add** button within the Output Keys section. 
![](./images/1-2-2-11.png)


Fill in the **Add Output Key** popup:
- **Name** - `url`

- **Type** - `string`

- Click on **Add** button to save it.
![](./images/1-2-2-12.png)

Your output **url** is ready to be consumed. 
![](./images/1-2-2-13.png)

Repeat same steps to add these outputs: 

- **Name** - `status`

- **Type** - `number`

AND 

- **Name** - `header`

- **Type** - `object`

AND

- **Name** - `body`

- **Type** - `string`

Your outputs are now in place. 
![](./images/1-2-2-14.png)

IMPORTANT: let's map the outputs to specific output values that will be populated along the command execution. To do so, click on **output**  within the Executors section followed by the **Edit** link: 
![](./images/1-2-2-16-1.png)

Update the values accordingly: 

- body: `$(.getAppEndpoint.output.body)`

- header: `$(.getAppEndpoint.output.headers)`

- status: `$(.getAppEndpoint.output.status)`

- url: `$(.getAppEndpoint.output.url)`

Click on **Update** button to save the changes.
![](./images/1-2-2-16-2.png)

https://xp267041-dev-bookshop-srv.cfapps.eu10-004.hana.ondemand.com/admin-books/webapp/index.html
Now let's trigger the command by clicking on the **Trigger** button
![](./images/1-2-2-15.png)

REMEBER: copy/paste your bookshop webapp index page from Excersie 1 within the `URL` parameter as we had left it required and clic on the button **Trigger**. 
![](./images/1-2-2-16.png)

The command has been completed succesfully! Click on the Output **Show** link to see the outputs returned by the command after its completion. 
![](./images/1-2-2-17.png)

You can validate and consume the output values returned by your command after being executed.



## Exercise 1.3 Setting up the integration between SAP Cloud ALM and SAP Automation Pilot

It is time to bring SAP Automation Pilot and SAP Cloud ALM together.

To do so we need to have in hand few values from SAP Automation Pilot that we'll use later in SAP Cloud ALM: 

In your SAP Automation Pilot, click on the **User** menu item (bottom left menu) and copy the values for: 

- Tenant ID

- Tenant URL
![](./images/01-2-01.png)

Click now the "API" menu item (bottom left menu) a copy **Base URL** value: 
![](./images/01-2-02.png)


Now we need to create a service accont so click on the menu item (lefside menu) Service Accounts --> **Create** button
![](./images/01-2-03.png)

From the pop-up, please add the following values: 

- username: `cloudALM`

- description: free text , i.e. `service account used in SAP Cloud ALM to trigger commands in SAP Automation Pilot`

- Permissions: from the drop-down select `Read` , `Write` , `Execute`

- Authentication Type: `Basic`

Once ready - click on the button **Create**
![](./images/01-2-04.png)

**IMPORTANT** Copy now the **Usarname** and the **Password** as once this screen gets closed, you won't be able to copy over again the password. 


![](./images/01-2-05.png)

Click on the **Close** button. Now you are all done. 

**Let's wrap-up** , now you should have copied from SAP Automation Pilot the values for: 
- Tenant ID

- Tenant URL

- Base URL

- Username

- Password

It is time to navigate to **SAP Cloud ALM** following this link: https://xp267-calm-1hdji9xc.eu10-004.alm.cloud.sap/

Click on **Operations** --> **Landscape Management**
![](./images/01-2-06.png)


Now click on **Services and Systems** menu icon from the left sidebar menu --> click on **Add New Serivce** (see screenshot below)
![](./images/01-2-07.png)

Now we need to fill in the data for **Add Service** screen as it follows: 

- **Name**: AP-XP267-0XX (as per user name, i.e. if your user is XP267-041 --> Name is AP-XP267-041) 

- **System Number**: {Tenant ID} (copied over from Automation Pilot, see previous step)

- **Service Type**: from the drop-down select `SAP Automation Pilot`

- **Role**: `Test`

- **Root URL**: {Tenant URL} (copied over from Automation Pilot, see previous step)

- **Deployment  Model**: from the drop-down select `BTP System`

Click on **Save** button
![](./images/01-2-08.png)

You should be able to see a new service like this one (see screenshot). Click on the Service name. 
![](./images/01-2-09.png)

Now we need to add an Endpoint on which we could call any command in SAP Automation Pilot. To do so Click on **Endpoints** tab --> **Add**
![](./images/01-2-10.png)

Fill in the data within the popup **Add Endpoint** as it follows: 

- **Endpoint Name**: same as the service name: AP-XP267-0XX (as per user name, i.e. if your user is XP267-041 --> Name is AP-XP267-041) 

- **Root URL**: {Base URL} (copied over from Automation Pilot, see previous step)

- **Root URL**: Keep Basic Authentication

- **User:** {username} (copied over from Automation Pilot, see previous step)

- **Password:** {password} (copied over from Automation Pilot, see previous step)

Click on **Save** button
![](./images/01-2-11.png)

Once saved, you shall see a screen like the one below. Click on **Ping Connection** button. 
![](./images/01-2-12.png)

Congrats - the endpoint check has been a successfull one. Now you can connect and use any command in SAP Automation Pilot from SAP Clod ALM. 
![](./images/01-2-13.png)

Your **SAP BTP subaccount is XP267-0XX** (use your hands-on session user e.g. XP266-001) and then click on **Services** dropdown from the lefside menu --> **Instances and Subscriptions** 
From the **Subscriptions** sections --> **Automation Pilot** (see the screenshot below)
![](./images/01-1-01.png)

You shall land SAP Automation Pilot **dashboard page**. 
![](./images/01-1-02.png)


Click on **Provided Catalogs** menu and check out all provided ready-to-use automation commands grouped in variois catalogs. These are 300+ ready-to-use commmands at your disposal. 
![](./images/01-1-03.png)

From the same menu you also can explore: 
- **My Catalogs** - a newly added commands at your disposal
- **Commands** - listing all your commands in a list
- **Inpits** - all available inpputs that can be referenced to any exisitng command

You are all set - it's time to move forward! 



## Summary

You've now ...

Continue to - [Exercise 2 - Exercise 2 Description](../ex2/README.md)

