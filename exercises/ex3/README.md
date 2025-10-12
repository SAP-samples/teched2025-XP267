
# Exercise 3 - Extending SAP Automation Pilot by an integration to SAP AI Core service for assessment of technical data and recommendations by AI

In this exercise, we will: 
- run and exent a command in SAP Automation Pilot desinged to collect application logs and Cloud Foundry environment's parameters 
- integrate SAP Automation Pilot to SAP AI Core service so that we would be able to provide the collected app logs and environment's data to AI and get assesment and recommendations by AI 
- get a summary by SAP AI Core about the assessment directly into SAP Automation Pilot and push this assessment into SAP Cloud ALM

For a better understanding of the currnet use case, please consult the diagram shared below: 
<img src="./images/ex-03-scenario.png" width="700" height="400">


## Exercise 2.1  Collect App logs and state with a command executed in SAP Automation Pilot 

**Access SAP Automation Pilot** and navigate via the left menu to **My Catalogs** --> click on **Commands** for the catalog `XP267 Ex03 - AI Powered Commands`

<img src="./images/03-01.png" width="700" height="400">

Navigate to the command named `AppLogsAnalysisByAI`
![](./images/03-02.png)

Thus command is already configured to collect the  logs , the current state and the latest events for you CAP application. 
**Trigger** the command , no any further inputs are needed. 
![](./images/03-03.png)

After the command succesful execution, consult the output values by clicking on **Show** link under Output. 
![](./images/03-04.png)

That's a current snapshot of CAP app logs, state and events.  You can take a moment to explore the information for a better understandig on what exectly happens with your app at the moment. 
![](./images/03-05.png)

Wouldn't be nice also to see the app memory state memory as an output key, too? That's possible - all we need to do is to add another output key to our existing command. To do so, navigate to the command **Output Keys** section and click on  **Add**. 
![](./images/03-06.png)

Within the Add Output Key popup, add the following: 

- **Name** - `stateMemory`

- **Type** - `number`
- 
Click on **Add** button
![](./images/03-07.png)

Now the output key is added but there is no output value to be exposed. To do so, within the command scroll down to the **Configuration** section, navigate to the **output** executor, click on it and then on **Edit** . 
![](./images/03-08.png)

Within the popup **Update Values** popup

- add the following into the output **stateMemory**: `$(.GetAppState.output.memory)`

- click **Update** button
![](./images/03-09.png)

Now **Trigger** again the command and consult the **Output** values after command completion. 

You shall see that the memory allocated within the space where your CAP app runs is **400 MB**. 
![](./images/03-10.png)




=========


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






## Summary

You've now learned the basic of SAP Automation Pilot, SAP Cloud ALM Health Monitoring and you have integrated the two products.

Continue to - [Exercise 2 - Exercise 2 Description](../ex2/README.md)
