# Exercise 2 – Collect Usage Metrics with SAP Automation Pilot and Push Them to SAP Cloud ALM

In this exercise, you will:  
- Collect usage metrics with **SAP Automation Pilot**  
- Use existing commands in SAP Automation Pilot to push these metrics to **SAP Cloud ALM**  
- View and analyze the custom metrics in **SAP Cloud ALM – Health Monitoring**

For a better understanding of the current use case, refer to the diagram below:  
<img src="./images/ex-02-scenario.png" width="700" height="400">

---

## Exercise 2.1 – Collect Usage Metrics with SAP Automation Pilot

1. Access **SAP Automation Pilot** and navigate to **My Catalogs → Commands** under the catalog:  
   `XP267 Ex02 – Custom Metrics into Cloud ALM`  
   <img src="./images/02-01.png" width="700" height="400">

2. Open the command named **`CheckResourceQuotaUtilizationXP267Extended`**.  
   ![](./images/02-02.png)

   This command is preconfigured to calculate the resource utilization of your **Cloud Foundry space** and its **parent organization**.

3. Click **Trigger** to execute the command (no input is required).  
   ![](./images/02-03.png)

4. After successful execution, click **Show** under **Output** to review the results.  
   ![](./images/02-04.png)

   The output displays the current snapshot of resource utilization for your Cloud Foundry space and organization.  
   ![](./images/02-05.png)

---

## Exercise 2.2 – Push Metrics to SAP Cloud ALM Using SAP Automation Pilot

Now, let’s extend the existing command to push these metrics into **SAP Cloud ALM – Health Monitoring**.

### Step 1 – Add a New Executor for Space-Level Metrics

1. Open the command and scroll down to the **Executors** section. Click **Add**.  
   ![](./images/02-06.png)

2. Configure the new executor:  
   - **Placement**: Just before the **Output** (click **Here**)  
   - **Alias**: `pushSpaceMemoryUtilization`  
   - **Command**: `PushCloudAlmQuotaXP267` (this command pushes quota data to SAP Cloud ALM metrics API)  
   - Keep **Automap parameters** enabled  
   - Click **Add**  
   ![](./images/02-07.png)

3. Edit the newly added executor `pushSpaceMemoryUtilization` to update its parameters:  
   ![](./images/02-08.png)

   **Set the following values:**  
   - **limit**: `$(.getCfSpaceQuota.output.body | toObject | .apps.total_memory_in_mb // 0)`  
   - **name**: `space.memory.utilization`  
   - **rating**:  
     ```
     $(.calculateSpaceMemoryPercentage.output.message | toNumber |
       if . > 95 then "fatal"
       elif . > 85 then "critical"
       elif . > 70 then "warning"
       elif . > 0 then "ok"
       else "error" end)
     ```
   - **serviceId**: `$(.execution.input.calmServiceId)`  
   - **unit**: `mb`  
   - **used**: `$(.calculateSpaceMemory.output.spaceMemory // 0)`  

   Click **Update** to save your changes.  
   ![](./images/02-09.png)

4. Validate that the parameters were saved correctly.  
   ![](./images/02-10.png)

---

### Step 2 – Add a New Executor for Organization-Level Metrics

Now, add another executor to push metrics for your **Cloud Foundry organization**.

1. Click **Add** in the **Executors** section.  
   ![](./images/02-11.png)

2. Configure the executor:  
   - **Placement**: Just before the **Output** (click **Here**)  
   - **Alias**: `pushOrgMemoryUtilization`  
   - **Command**: `PushCloudAlmQuotaXP267`  
   - Keep **Automap parameters** enabled  
   - Click **Add**  
   ![](./images/02-12.png)

3. Edit the `pushOrgMemoryUtilization` executor and update the parameters:  
   ![](./images/02-13.png)

   **Set the following values:**  
   - **limit**: `$(.getCfOrgQuota.output.body | toObject | .apps.total_memory_in_mb // 0)`  
   - **name**: `org.memory.utilization`  
   - **rating**:  
     ```
     $(.calculateOrgMemoryPercentage.output.message | toNumber |
       if . > 95 then "fatal"
       elif . > 85 then "critical"
       elif . > 70 then "warning"
       elif . > 0 then "ok"
       else "error" end)
     ```
   - **serviceId**: `$(.execution.input.calmServiceId)`  
   - **unit**: `mb`  
   - **used**: `$(.getCfOrgUsage.output.body | toObject | .usage_summary.memory_in_mb // 0)`  

   Click **Update** to save your changes.  
   ![](./images/02-14.png)

4. Verify the parameters of the `pushOrgMemoryUtilization` executor.  
   ![](./images/02-15.png)

---

### Step 3 – Trigger the Command

1. Click **Trigger** (top-right corner). No additional input is required.  
   ![](./images/02-16.png)

2. ✅ **Success!** The command executes successfully, and data is pushed to **SAP Cloud ALM**.  
   ![](./images/02-17.png)

Next, let’s verify the metrics in SAP Cloud ALM.

---

## Exercise 2.3 – Consume Custom Metrics in SAP Cloud ALM – Health Monitoring

1. Access **SAP Cloud ALM**:  
   [https://xp267-calm-1hdji9xc.eu10-004.alm.cloud.sap/](https://xp267-calm-1hdji9xc.eu10-004.alm.cloud.sap/)

2. Log in with your user, then navigate to **Operations → Health Monitoring**.  

3. From the Health Monitoring Overview, click **Monitoring**.  
   ![](./images/02-18.png)

4. Click on your **SAP BTP Cloud Foundry environment** (e.g. `XP267_0XX_CF`).  
   ![](./images/02-19.png)

5. You’ll arrive at the **Metrics Overview** screen.  
   Scroll down to the **Other Metrics** section to find:  
   - `org.memory.utilization`  
   - `space.memory.utilization`  

   Both metrics should display values pushed from SAP Automation Pilot.  
   ![](./images/02-20.png)

6. Click on one of the metrics (for example, `space.memory.utilization`) to view details such as its current value, rating, and metadata.  
   Then, click **History** to explore how the metric evolved over time.  
   ![](./images/02-21.png)  
   ![](./images/02-22.png)

🎉 **Congratulations!**  
You can now automatically feed SAP Cloud ALM’s observability layer with any custom metrics you define in SAP Automation Pilot.

---

## Summary

You have successfully:  
- Collected and analyzed **usage metrics** from your SAP BTP subaccount  
- Extended commands in **SAP Automation Pilot** by adding new executors  
- Pushed custom operational metrics to **SAP Cloud ALM – Health Monitoring**  
- Verified and explored these metrics within the observability dashboards  

Proceed to the next step:  
➡️ [Exercise 3 – Exercise 3 Description](../ex3/README.md)
