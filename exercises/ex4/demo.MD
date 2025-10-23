## Demo #1 - Push metrics to CALM 

#### As a starting point: Run the command 

#### Add the Executor 
- **Place the new executor** just before the Ouput by clickin on **Here** botton.

- **Alias** - `pushSpaceMemoryUtilization` 

- **Command** - `PushCloudAlmQuotaXP267` - that's a command in SAP Automation Pilot that pushes quota data directly into SAP Cloud ALM metrics API.

- **Keep enabled** the `Automap parameters`

- Click on **Add** button


#### Edit Executor's parameters
Now navigate to the executor you just created `pushSpaceMemoryUtilization` and click on the **Edit** button to update its parameters. 

Update the values accordingly: 

- **limit** - `$(.getCfSpaceQuota.output.body | toObject | .apps.total_memory_in_mb // 0)`

- **name** - `space.memory.utilization`

- **rating** - `$(.calculateSpaceMemoryPercentage.output.message | toNumber | if . > 95 then "fatal" elif . > 85 then "critical" elif . > 70 then "warning" elif . > 0 then "ok" else "error" end)`

- **serviceId** - `$(.execution.input.calmServiceId)`

- **unit** - `mb`

- **used** - `$(.calculateSpaceMemory.output.spaceMemory // 0)`

Click on the **Update** button. 



## Demo #2 - Aanalyze logs with AI and push to CALM

### Add AI Insights Focused on Memory Consumption

Add an AI analysis specialized in **memory utilization**, plus a **short summary**.

1. Add an executor:  
   - **Alias**: `AICoreMemoryAnalysis`  
   - **Command**: `GenerateGpt4OmniCompletion`  
   - **Placement**: before **Output**  
   - Keep **Automap parameters** enabled  
   - Click **Add**  

2. Edit **`AICoreMemoryAnalysis`** and set:  
   - **deploymentId**: `$(.AICoreData.deploymentId)`  
   - **prompt**:  
     ```
     Available memory allocated within the Cloud Foundry space = "$(.GetAppState.output.memory) MB" and App logs are $(.AICoreAnalyze.output.response)
     ```
   - **serviceKey**: `$(.AICoreData.serviceKey)`  
   - **systemMessage**:  
     ```
     You are an AI assistant with expertise in CAP NodeJS applications. You will receive as inputs the available memory allocated within the Cloud Foundry space where the app runs and the logs from the same CAP NodeJS application. Analyze these inputs and provide an assessment of the app's memory utilization with recommendations focused only on memory utilization.
     ```

   Click **Update**.

3. Add another executor for a **very short memory summary** (≤ 3 words):  
   - **Alias**: `AICoreMemoryAnalyzeSummary`  
   - **Command**: `GenerateGpt4OmniCompletion`  
   - **Placement**: before **Output**  
   - Keep **Automap parameters** enabled  
   - Click **Add**  

4. Edit **`AICoreMemoryAnalyzeSummary`** and set:  

   - **deploymentId**: `$(.AICoreData.deploymentId)`  
   - **prompt**:  
     ```
     Available memory allocated within the Cloud Foundry space = "$(.GetAppState.output.memory) MB" and App logs are $(.AICoreAnalyze.output.response)
     ```
   - **serviceKey**: `$(.AICoreData.serviceKey)`  
   - **systemMessage**:  
     ```
     You are an AI assistant with expertise in CAP NodeJS applications. You will receive the available memory for the Cloud Foundry space and the app logs. Analyze and provide an assessment of the app's memory utilization summarized in up to 3 words.
     ```

   Click **Update**.

5. Add **Output Keys** and map them:  
   ![](./images/03-38.png)

   - **AIOutputMemory** (`string`) → `$(.AICoreMemoryAnalysis.output.response)`  
   - **AIOutputMemorySummary** (`string`) → `$(.AICoreMemoryAnalyzeSummary.output.response)`  

   Save the mappings in the **output** executor.  

6. **Trigger** the command and check **Output** for both memory fields.  

