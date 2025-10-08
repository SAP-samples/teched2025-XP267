# Excercise 0 - Get to know your environment and CF App

In this exercise, you will get to know your SAP BTP account the CAP app running on your Cloud Foundry environment.

## Access to your Cloud Foundry 

After completing these steps you will have an understanding on the CF demo environment and CAP app running for this hands on.
*IMPORTANT* the CAP application is a CAP node.js app that is already deployed and instumented with an OTEL library (needed to push metrics to SAP Cloud ALM). 

Access your BTP subaccount
Your SAP BTP subaccount is XP267-0XX (use your hands-on session user e.g. XP266-001) and then click on **Cloud Foundry** dropdown from the lefside menu --> **Spaces**
![](./images/00-01.png)

Click on "dev" - that's you dev CF space. 
![](./images/00-02.png)

You will land at the applications running on your "dev" CF space. There is an app named `bookshop-srv` with status "Started"
Take a look and check the paramters visible - as you can notice, the memory allocated per instance is 400 MB. 
![](./images/00-03.png)

Now click on the the app itslef `bookshop-srv` and you will see the app overview tab. Take a look - you will notice that the app uses a bit less than 400MB memory while the memory allocated to all istances is 400MB. 
![](./images/00-03-1.png)

Click on the applicaiton route link to access your app cds Server page. 
![](./images/00-04.png)

From this screen, click on /admin-books/webapp/index.html to access the  admin books webapp. 
![](./images/00-05.png)

Expected result is that you shall land to a page like this one: 
![](./images/00-06.png)


## Summary

Now you had verified that the CF app runs sucesfully on your CF runtime. You also had seen that there are 400 MB allocated to your app and it currently consumes a bit less than this limit. 
Continue to - [Exercise 1 - Exercise 1 Description](../ex1/README.md)
