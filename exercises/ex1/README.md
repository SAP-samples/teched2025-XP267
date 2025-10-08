# Exercise 1 - Access SAP Cloud ALM and trigger tigger simple commands to your App via SAP Automation Pilot 

In this exercise, we will: 
- access SAP Cloud ALM and explore the metrics visible in Health Monitoring
- interact with App endpoints via SAP Automation Pilot
- setup the integration between SAP Cloud ALM and SAP Automation Pilot

For a better understanding of the currnet use case, please consult the diagram shared below: 
![](./images/ex-01-scenario.png)

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

After completing these steps you will have...

1.	Enter this code.
```abap
DATA(lt_params) = request->get_form_fields(  ).
READ TABLE lt_params REFERENCE INTO DATA(lr_params) WITH KEY name = 'cmd'.
  IF sy-subrc <> 0.
    response->set_status( i_code = 400
                     i_reason = 'Bad request').
    RETURN.
  ENDIF.

```

2.	Click here.
<br>![](/exercises/ex1/images/01_02_0010.png)

## Exercise 1.3 Setup the integration between SAP Cloud ALM and SAP Automation Pilot 

After completing these steps you will have...


## Summary

You've now ...

Continue to - [Exercise 2 - Exercise 2 Description](../ex2/README.md)

