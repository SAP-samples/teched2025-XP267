*Demo #1*

Add the Executor 
- **Place the new executor** just before the Ouput by clickin on **Here** botton.

- **Alias** - `pushSpaceMemoryUtilization` 

- **Command** - `PushCloudAlmQuotaXP267` - that's a command in SAP Automation Pilot that pushes quota data directly into SAP Cloud ALM metrics API.

- **Keep enabled** the `Automap parameters`

- Click on **Add** button
![](./images/02-07.png)

Now navigate to the executor you just created `pushSpaceMemoryUtilization` and click on the **Edit** button to update its parameters. 
![](./images/02-08.png)

Update the values accordingly: 

- **limit** - `$(.getCfSpaceQuota.output.body | toObject | .apps.total_memory_in_mb // 0)`

- **name** - `space.memory.utilization`

- **rating** - `$(.calculateSpaceMemoryPercentage.output.message | toNumber | if . > 95 then "fatal" elif . > 85 then "critical" elif . > 70 then "warning" elif . > 0 then "ok" else "error" end)`

- **serviceId** - `$(.execution.input.calmServiceId)`

- **unit** - `mb`

- **used** - `$(.calculateSpaceMemory.output.spaceMemory // 0)`

Click on the **Update** button. 
![](./images/02-09.png)
