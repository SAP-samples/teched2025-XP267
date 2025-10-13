
# Exercise 3 - Extending SAP Automation Pilot by an integration to SAP AI Core for recommendations by AI

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

Click on the AIOutput arrow and you can read the insights provided by the SAP AI Core service based on the current snapshot of your app logs. 
![](./images/03-21.png)


## Exercise 3.3  Extend existing SAP Automation Pilot with a summary assessment by SAP AI Core service 

Now, since we have in place an intgeration with SAP AI Core service, let's play and adjust the  insights and the recommendations provided.
We can do so by adjusting the prmpts and the system messages we pass to the AI so that the response will be a different one. 

Since the analysis from the first step might be quite long, let's try to summarize it and make it much shorter so that we can push this summary directly into SAP Cloud ALM - Health Monitoring.

To do so: we'll need to extent the same command - `AppLogsAnalysisByAI`. 

**Go to the command itself**, scroll-down to the **Executor section** and click on **Add** button. 

![](./images/03-23.png)

Add the Executor 
- **Place the new executor** just before the Ouput by clickin on **Here** botton.

- **Alias** - `AICoreAnalyzeSummary` 

- **Command** - `GenerateGpt4OmniCompletion` - that's a provided command in SAP Automation Pilot that can push data to SAP AI Core service (Gpt 4o mini model) and provide the response.

- **Keep enabled** the `Automap parameters`

- Click on **Add** button
![](./images/03-24.png)

Now navigate to the executor you just created `AICoreAnalyze` and click on the **Edit** button to update its parameters. 
![](./images/03-25.png)

Update the values accordingly: 

- **deploymentId** - `$(.AICoreData.deploymentId)`

- **prompt** - `$(.AICoreAnalyze.output.response)` --> we are passing the AI Core analysis from the previous step

- **serviceKey** - `$(.AICoreData.serviceKey)`

- **systemMessage** - `You are an AI assistant with expertise in CAP NodeJS applications. You will receive an analysis of the logs from a CAP NodeJS application running on Cloud Foundry space. Your task is to analyze further the inputs and provide your assessment about the app health in just one status (use one out of these statuses as a single word with without any formatting or any other chars applied to the status: OK, Investigate, Critical).`

Click on the **Update** button. 
![](./images/03-26.png)

Navigate to the executor `AICoreAnalyze` and validate the paramters you just had added. It should look like this one:
![](./images/03-27.png)

What baisically we just did is that we are getting the AI Core from the previous step and we are asking to the model to summarize it and to lavel it with just a single status which can be ingested and used directly into SAP Cloud ALM on a later stage. Pay attention into the system message we passed to the model: _"You are an AI assistant with expertise in CAP NodeJS applications. You will receive an analysis of the logs from a CAP NodeJS application running on Cloud Foundry space. Your task is to analyze further the inputs and provide your assessment about the app health in just one status (use one out of these statuses as a single word with without any formatting or any other chars applied to the status: OK, Investigate, Critical)."_

As we already had learned in the previous execrcise, to consume an output value out of a command executed in SAP Automation Pilot we need to add an Output Key. 

Within the command itself, Go now to the **Output Key** section and click on **Add**.
![](./images/03-28.png)

Within the popup Add Output Key provide the following attributes: 

- **Name** - `AIOutputSummary`

- **Type** - `string`

  Click on **Add** button.
  ![](./images/03-29.png)

IMPORTANT: let's map the outputs to specific output values that will be populated along the command execution. To do so, click on **output**  within the Executors section followed by the **Edit** link: 
![](./images/03-30.png)

Update the values accordingly: 

- **AIOutput**: `$(.AICoreAnalyzeSummary.output.response)`

Click on **Update** button to save the changes.
![](./images/03-31.png)

**Trigger the command in SAP Automation Pilot**
Now it is all set and you can trigger the command in SAP Automation Pilot. 
To do so, click on the **Trigger** button located in the the top righ screen and proceed further as no any further inputs are needed.
![](./images/03-32.png)

The command has been completed succesfully! Click on the Output **Show** link to see the outputs returned by the command after its completion. 
![](./images/03-33.png)

Success - you see now the output value within `AIOutputSummary` providing a single status insight about the health status of your app powered by AI based on the current snapshot of your app logs. 
![](./images/03-34.png)

As you see the status is `Investigate` so you shall do further investigation. 

## Exercise 3.4  Extend existing SAP Automation Pilot winsights powerd by SAP AI Core service with a special focus on memory consumption

We already know that the app consumes memory so let's add a logic and assesment by AI with a special focus on app memory consumption. 

To do so, we'll need to extent the command from the previous step - `AppLogsAnalysisByAI`. 
Hint: That's pretty much same approach as per the previous steps but the parameters are changed. Follow the steps as you shall already quite familiar with the interface from SAP Automation Pilot

**Go to the command itself**, scroll-down to the **Executor section** and click on **Add** button. 

Add the Executor 
- **Place the new executor** just before the Ouput by clickin on **Here** botton.

- **Alias** - `AICoreMemoryAnalysis` 

- **Command** - `GenerateGpt4OmniCompletion` - that's a provided command in SAP Automation Pilot that can push data to SAP AI Core service (Gpt 4o mini model) and provide the response.

- **Keep enabled** the `Automap parameters`

- Click on **Add** button
![](./images/03-35.png)

Now navigate to the executor you just created `AICoreMemoryAnalysis` and click on the **Edit** button to update its parameters. 

Update the values accordingly: 

- **deploymentId** - `$(.AICoreData.deploymentId)`

- **prompt** - `Available memory allocated within the Cloud Foundry space = "$(.GetAppState.output.memory) MB" and App logs are $(.AICoreAnalyze.output.response)` 

- **serviceKey** - `$(.AICoreData.serviceKey)`

- **systemMessage** - `You are an AI assistant with expertise in CAP NodeJS applications. You will receive as inputs the available memory allocated within the Cloud Foundry where the app runs and also  the logs from the same CAP NodeJS application running on Cloud Foundry space. Your task is to analyze these inputs and prepare an assessment of the app memory utilisation providing also recommendations only related to the app memory utilisation topic.`

Click on the **Update** button. 


What baisically we just did is that we are getting the available mememory allocated in our  Cloud Foundry space and along with the logs logs collected for our app  we pass this information to the SAP AI Core service (Gpt 4o mini model)  asking for an assessment as we also send a system messag as well: _"You are an AI assistant with expertise in CAP NodeJS applications. You will receive as inputs the available memory allocated within the Cloud Foundry where the app runs and also  the logs from the same CAP NodeJS application running on Cloud Foundry space. Your task is to analyze these inputs and prepare an assessment of the app memory utilisation providing also recommendations only related to the app memory utilisation topic."_

Let's also add another executor. 

Since the analysis from the first step might be a bit longer, let's try to summarize it and make it much shorter so that we can push this summary directly into SAP Cloud ALM - Health Monitoring.

To do so: we'll need to extent the same command - `AppLogsAnalysisByAI`. 

**Go to the command itself**, scroll-down to the **Executor section** and click on **Add** button. 

Add the Executor: 
- **Place the new executor** just before the Ouput by clickin on **Here** botton.

- **Alias** - `AICoreMemoryAnalyzeSummary` 

- **Command** - `GenerateGpt4OmniCompletion` - that's a provided command in SAP Automation Pilot that can push data to SAP AI Core service (Gpt 4o mini model) and provide the response.

- **Keep enabled** the `Automap parameters`

- Click on **Add** button
![](./images/03-36.png)

Now navigate to the executor you just created `AICoreMemoryAnalyzeSummary` and click on the **Edit** button to update its parameters. 
![](./images/03-25.png)

Update the values accordingly: 

- **deploymentId** - `$(.AICoreData.deploymentId)`

- **prompt** - `Available memory allocated within the Cloud Foundry space = "$(.GetAppState.output.memory) MB" and App logs are $(.AICoreAnalyze.output.response)` 

- **serviceKey** - `$(.AICoreData.serviceKey)`

- **systemMessage** - `You are an AI assistant with expertise in CAP NodeJS applications. You will receive as inputs the available memory allocated within the Cloud Foundry where the app runs and also  the logs from the same CAP NodeJS application running on Cloud Foundry space. Your task is to analyze these inputs and prepare an assessment of the app memory utilisation only summarising in up to 3 words.`

Click on the **Update** button. 

What baisically we just did is that we are getting the AI Core from the previous step and we are asking the model to summarize and prepare an assessment in up to 3 words. This assessment can be ingested and used directly into SAP Cloud ALM on a later stage. Pay attention into the system message we passed to the model: _"You are an AI assistant with expertise in CAP NodeJS applications. You will receive as inputs the available memory allocated within the Cloud Foundry where the app runs and also  the logs from the same CAP NodeJS application running on Cloud Foundry space. Your task is to analyze these inputs and prepare an assessment of the app memory utilisation only summarising in up to 3 words."_

As we already had learned in the previous execrcise, to consume an output value out of a command executed in SAP Automation Pilot we need to add an Output Key. 

Within the command itself, Go now to the **Output Key** section and click on **Add**.
![](./images/03-38.png)

Within the popup **Add Output Key** provide the following attributes: 

- **Name** - `AIOutputMemory`

- **Type** - `string`

  Click on **Add** button.
  
Add another outout key following the same steps: 

Within the popup **Add Output Key** provide the following attributes: 

- **Name** - `AIOutputMemorySummary`

- **Type** - `string`

  Click on **Add** button.

IMPORTANT: let's map the outputs to specific output values that will be populated along the command execution. To do so, click on **output**  within the Executors section followed by the **Edit** link: 
![](./images/03-39.png)

Update the values accordingly: 

- **AIOutputMemory**: `$(.AICoreMemoryAnalysis.output.response)`

- **AIOutputMemorySummary**: `$(.AICoreMemoryAnalyzeSummary.output.response)`

Click on **Update** button to save the changes.
![](./images/03-40.png)

**Trigger the command in SAP Automation Pilot**
Now it is all set and you can trigger the command in SAP Automation Pilot. 
To do so, click on the **Trigger** button located in the the top righ screen and proceed further as no any further inputs are needed.
![](./images/03-41.png)

The command has been completed succesfully! Click on the Output **Show** link to see the outputs returned by the command after its completion. 
![](./images/03-42.png)

Success - you see now the output value within `AIOutputMemory` and `AIOutputMemorySummary` longer and short app memory analysis powered by AI based. 
![](./images/03-43.png)


## Exercise 3.5  Use existing commands in SAP Automation Pilot to push these metrics to SAP Cloud ALM - Health Monitoring

Now we want to push these metrics into SAP Cloud ALM - Health Monitoring. 

To do so, we'll need to extent this existing command. As we already had used provided content in SAP Automation Pilot to do so (see ## Exercise 2.2) we can jump directly into an extended command. 

From the letf menu go to: My Catalogs --> Catalog `XP267 Ex03 - AI Powered Commands` and click on **Commands**
![](./images/03-44.png)

Then click on command `AppLogsAnalysisByAIExtendedPushToCloudALM`
![](./images/03-45.png)

IMPORTANT: make sure you had navigated to the command specified: `AppLogsAnalysisByAIExtendedPushToCloudALM`.

It is the same command we just worked on but it is extended with two additional executors: 

- `pushAppSummary` - it pushes to SAP Cloud ALM the output value out of the executor `AICoreAnalyzeSummary` we had created in the previous steps. Basically that's the single word status for the app health check.
![](./images/03-46.png)

- `pushMemorySummary` - it pushes to SAP Cloud ALM the output value out of the executor `AICoreMemoryAnalyzeSummary` we had created in the previous steps. Basically that's three words status for the app memory consumptions analysis and recommendations.
![](./images/03-47.png)

 
**Trigger the command in SAP Automation Pilot**
Now it is all set and you can trigger the command in SAP Automation Pilot. 
To do so, click on the **Trigger** button located in the the top righ screen and proceed further as no any further inputs are needed.

The command has been completed succesfully! Click on the Output **Show** link to see the outputs returned by the command after its completion. 

**Success** - the command has been completed and the data has been pushed to SAP Cloud ALM. 
![](./images/03-49.png)


## Exercise 3.6 Access SAP Cloud ALM and explore the metrics visible in Health Monitoring

Now it's time to check the result in SAP Cloud ALM. 

**Access SAP Cloud ALM** by following this link here: https://xp267-calm-1hdji9xc.eu10-004.alm.cloud.sap/

**Login** with your user 
Select the **Operations** menu item. 

Click on **Health Monitoring** capability. 

You will land at Health Monitoring Overview screen. 
Make sure Scope has been selected to your subaccount XP267-0XX_CF (e.g. XP267_000_CF)

Click on the tile and you shall be able to see the services/ system visible in the Health Monitoring. 
![](./images/03-48.png)

Click on the service to land to the SAP BTP Cloud Foundry environment **Metrics Overview**. 

Take a look into the section "**Other Metrics** and you will find two new metrics: 

- `app.memory.ai`

- `app.status.ai`

Congrats! These are the ones we just pushed from SAP Automation Pilot directly into SAP Cloud ALM. Now you are all set and you can consume insights direcltly into **Metrics Overview dashboard** in SAP Cloud ALM - **Health Monitoring**. 
![](./images/03-50.png)


## Summary

You've now learned the basics and more extended usage of the interplay betweenSAP Automation Pilot, SAP AI Core service and SAP Cloud ALM Health Monitoring to push real time insights powered by AI model directly into **Metrics Overview dashboard** in SAP Cloud ALM - **Health Monitoring**.

Continue to - [Exercise 4 - Exercise 4 Description](../ex4/README.md)
