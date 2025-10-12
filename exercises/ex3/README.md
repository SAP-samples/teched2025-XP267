
# Exercise 3 - Extending SAP Automation Pilot by an integration to SAP AI Core service for assessment of technical data and recommendations by AI

In this exercise, we will: 
- run and exent a command in SAP Automation Pilot desinged to collect application logs and Cloud Foundry environment's parameters 
- integrate SAP Automation Pilot to SAP AI Core service so that we would be able to provide the collected app logs and environment's data to AI and get assesment and recommendations by AI 
- get a summary by SAP AI Core about the assessment directly into SAP Automation Pilot and push this assessment into SAP Cloud ALM

For a better understanding of the currnet use case, please consult the diagram shared below: 
<img src="./images/ex-03-scenario.png" width="700" height="400">


## Exercise 3.1  Collect App logs and state with a command executed in SAP Automation Pilot 

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


## Exercise 3.2  Extend existing SAP Automation Pilot with an integration to SAP AI Core service for assesment and recommendations by AI 

Now, since we have all the needed informaiton to do an assessment of the health status for our CAP application, let's bring SAP AI Core service. Main idea is this assessment to be made by AI based on the current snapshot of the app logs and environment's parametes. 

Of course, we'll use existing command in SAP Automation Pilot to push the data collected and a tailored prompt to SAP AI Core service and the consume its response. 
_Note: an SAP AI Core service, AI model deployment has been already made and `ServiceKey` needed for authentication has been added to you SAP Automation Pilot tenant. _


 To do so, we'll need to extent the command from the previous step - `AppLogsAnalysisByAI`. 

**Go to the command itself**, scroll-down to the **Executor section** and click on **Add** button. 

Add the Executor 
- **Place the new executor** just before the Ouput by clickin on **Here** botton.

- **Alias** - `AICoreAnalyze` 

- **Command** - `GenerateGpt4OmniCompletion` - that's a provided command in SAP Automation Pilot that can push data to SAP AI Core service (Gpt 4o mini model) and provide the response.

- **Keep enabled** the `Automap parameters`

- Click on **Add** button
![](./images/03-11.png)

Now navigate to the executor you just created `AICoreAnalyze` and click on the **Edit** button to update its parameters. 
![](./images/03-12.png)

Update the values accordingly: 

- **deploymentId** - `$(.AICoreData.deploymentId)`

- **prompt** - `$(.GetAppLogs.output.logs)`

- **serviceKey** - `$(.AICoreData.serviceKey)`

- **systemMessage** - `You are an AI assistant with expertise in CAP NodeJS applications. You will receive application logs from a CAP NodeJS application that uses HANA Cloud as its database. Your task is to analyze these logs and provide a summary highlighting the application's current state and any potential issues.`

Click on the **Update** button. 
![](./images/03-13.png)

Navigate to the executor `AICoreAnalyze` and validate the paramters you just had added. It should look like this one:
![](./images/03-14.png)

What baisically we just did is that we are getting the app logs collected and we passing these to the SAP AI Core service (Gpt 4o mini model) and we are asking it for an assessment as we send a system messag as well: _"You are an AI assistant with expertise in CAP NodeJS applications. You will receive application logs from a CAP NodeJS application that uses HANA Cloud as its database. Your task is to analyze these logs and provide a summary highlighting the application's current state and any potential issues."_

It's time to explore the output and what assessment the AI Core service will return. 
As we already had learned in the previous execrcise, to consume an output value out of a command executed in SAP Automation Pilot we need to add an Output Key. 

Within the command itself, Go now to the **Output Key** section and click on **Add**.
![](./images/03-16.png)

Within the popup Add Output Key provide the following attributes: 

- **Name** - `AIOutput`

- **Type** - `string`

  Click on **Add** button.
  ![](./images/03-17.png)

IMPORTANT: let's map the outputs to specific output values that will be populated along the command execution. To do so, click on **output**  within the Executors section followed by the **Edit** link: 
![](./images/03-18.png)

Update the values accordingly: 

- **AIOutput**: `$(.AICoreAnalyze.output.response)`

Click on **Update** button to save the changes.
![](./images/03-19.png)

**Trigger the command in SAP Automation Pilot**
Now it is all set and you can trigger the command in SAP Automation Pilot. 
To do so, click on the **Trigger** button located in the the top righ screen and proceed further as no any further inputs are needed.
![](./images/03-15.png)

The command has been completed succesfully! Click on the Output **Show** link to see the outputs returned by the command after its completion. 
![](./images/03-20.png)

Click on the AIOutput arrow and you can get the insights provided by the SAP AI Core service based on the current snapshot of your app logs. 
![](./images/03-21.png)


## Exercise 3.3  Extend existing SAP Automation Pilot with a summary assessment by SAP AI Core service 

Now, since we have all the needed informaiton to do an assessment of the health status for our CAP application, let's bring SAP AI Core service. Main idea is this assessment to be made by AI based on the current snapshot of the app logs and environment's parametes. 







You can validate and consume the output values returned by your command after being executed.


Now we need to add another executor next to it. The executor is about pushing the current usage for all our CF spaces on Org level (in case we do have more than one CF Space). 

To do so, go to the executors section in your command and click on the **Add** button to add a new executro within you automation flow. 
![](./images/02-11.png)

Add the Executor 
- **Place the new executor** just before the Ouput by clickin on **Here** botton.

- **Alias** - `pushOrgMemoryUtilization` 

- **Command** - `PushCloudAlmQuotaXP267` - that's a command in SAP Automation Pilot that pushes quota data directly into SAP Cloud ALM metrics API.

- **Keep enabled** the `Automap parameters`

- Click on **Add** button
![](./images/02-12.png)

Now navigate to the executor you just created `pushOrgMemoryUtilization` and click on the **Edit** button to update its parameters. 
![](./images/02-13.png)

Update the values accordingly: 

- **limit** - `$(.getCfOrgQuota.output.body | toObject | .apps.total_memory_in_mb // 0)`

- **name** - `org.memory.utilization`

- **rating** - `$(.calculateOrgMemoryPercentage.output.message | toNumber | if . > 95 then "fatal" elif . > 85 then "critical" elif . > 70 then "warning" elif . > 0 then "ok" else "error" end)`

- **serviceId** - `$(.execution.input.calmServiceId)`

- **unit** - `mb`

- **used** - `$(.getCfOrgUsage.output.body | toObject | .usage_summary.memory_in_mb // 0)`

Click on the **Update** button. 
![](./images/02-14.png)

Navigate to the executor `pushOrgMemoryUtilization` and validate the paramters you just had added. It should look like this one:
![](./images/02-15.png)

**Trigger the command in SAP Automation Pilot**
Now it is all set and you can trigger the command in SAP Automation Pilot. 
To do so, click on the **Trigger** button located in the the top righ screen and proceed further as no any further inputs are needed.
![](./images/02-16.png)

**Success** - the commands has been completed and data is pushed to SAP Cloud ALM! 
![](./images/02-17.png)











Thus command is already configured to collect the  logs , the current state and the latest events for you CAP application. 
**Trigger** the command , no any further inputs are needed. 
![](./images/03-03.png)




## Exercise 3.2  Extend the command in SAP Automation Pilot with insights provided by SAP AI Core

**Access SAP Automation Pilot** - same command from the previous step - `AppLogsAnalysisByAI`. 

So far wr 





====================

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
