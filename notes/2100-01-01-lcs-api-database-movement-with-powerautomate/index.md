# LCS API Database Movement with PowerAutomate


- [1. PowerAutomate Sample - LCS API - Automatic refresh database](#1-powerautomate-sample---lcs-api---automatic-refresh-database)
- [2. LCS API - Export database](#2-lcs-api---export-database)
- [## 3. Retrieve list of database backup](#-3-retrieve-list-of-database-backup)

Hello Dynamics & Power Community,

I’m very happy to announce a good news for you the Dynamics365 F&O Community. I’ve got a little gift for you, as part of the new LCS API.

If you were, like me as an AX 2012 or F&O Technical/Architect Consultant, you probably already suffered from not being able to offer customers the ability to automatically refresh your database from one environment to other. Of course, you could do it via scripts, powershell (but really complicated for LCS and F&O : see even impossible), but finally doing it by code actually…. and here I will show you the benefit of the PowerAutomate / Flow to refresh automatically your F&O production instance to an UAT instance, without any line of code !

I will provide you, the How-To setup here in this article (step-by-step), also 3 samples of my GitHub account for PowerAutomate flow.

Hope you will like it, and feel free to share it :)

![LCS_API_DatabaseMovement.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586893028903-NTI0IKAN11ZPW93WX7GP/ke17ZwdGBToddI8pDm48kB22_VvoyFCRbPbLc__z6BN7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z4YTzHvnKhyp6Da-NYroOW3ZGjoBKy3azqku80C789l0mCjggly5cZuu_YqpnrzBi6yTqBkwjrVCaBTX1Yp4Xgg6k6KMOdln3GErxXMetT4lA/LCS_API_DatabaseMovement.png)

So…. how to start :)

First of all, here is a quick summary of the new API for LCS - in Dynamics 365 F&O.

Like you know, we were not able to perform automatic Database movement in the past. We get only to an Azure Marketplace a flow between Azure DevOps release pipeline in order to upload your BUILD package and also setup an automatic deployment. It was the very beginning to perform great automatic release pipelines : like me, deploying every night for customers a recent package in our TEST environments every night after BUILD completed.

Database movement operations are a suite of self-service actions that can be used as part of Data Application Lifecycle Management (also referred to as _DataALM_). These actions provide structured processes for common implementation scenarios such as golden configuration promotion, debugging/diagnostics, destructive testing, and general refresh for training purposes.

In this topic, you will learn how to use database movement operations to perform refresh, export, import, and various flavors of point-in-time restore.

At the time, I write this article, the LCS API have only 4 methods but I hope Microsoft will add soon more methods and of course I will share that with you depending on which and when it will be possible.

We can perform the following operations through the RESTful API:

*   List DB backups for an LCS project.
    
*   Create a DB refresh between two environments (only Prod and Sandbox environments, like we can do in LCS).
    
*   Get an ongoing operation status.
    

Quick feedbacks for Microsoft : we really need to add also monitoring KPI stuff methods in order to get the DTU SQL Percentage Live for production or other stuff like that, in order to not connect every time in the LCS Portal and check if everything looks good : to be proactive in fact / and maybe also restart service etc… : but yep I maybe ask a lot, it’s a good start now that this API is open, so I know that Microsoft will bring this API even better this year !

[Microsoft Documentation - LCS API](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/database/api/dbmovement-api-overview?WT.mc_id=BA-MVP-5003744)

* * *

First, you will need to create an Azure AAD in order to connect to the LCS API.

You can go here for the How-To setup :

[https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/database/api/dbmovement-api-authentication](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/database/api/dbmovement-api-authentication?WT.mc_id=BA-MVP-5003744)  

Don’t forget to copy somewhere your ClientID : ApplicationID and also to generate a ClientSecret. Also don’t forget to put the Dynamics Lifecycle Services like below as a Delegated access, with Grant Admin permission.

![Screenshot 2020-04-14_21-57-02-177.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586894286172-KZ2TY6UYVPG3KYNQGZWK/ke17ZwdGBToddI8pDm48kIYsmS_RYdETrfsiJtoZgiN7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z5QPOohDIaIeljMHgDF5CVlOqpeNLcJ80NK65_fV7S1UbKt62EjeZyxnEnVFQmB361CheMshuWChtkz_Oo9DJqtP7cJNZlDXbgJNE9ef52e8w/Screenshot+2020-04-14_21-57-02-177.png)

So, now you have the access to call the LCS API, and you can authenticate to it. But maybe, like me, if you had in the past configure the AzureDevOps release pipeline process to connect to LCS, you had maybe already an App Registration in your Azure AD, so you can keep it and reuse for this purpose.

* * *

Just for your information, It was not possible to create directly for you Custom Connectors in PowerAutomate in order to deploy it more easily. Since the LCS API use at the moment a strange and old school authentification. Hope I can share that when OAuth 2.0 will be used for this API !). But these samples will be enough and easy to configure, trust me !

Now, you can get my PowerAutomate / Flow samples directly in my Github Account :

Download all the 3 ZipFiles  

[ALM LCS PowerAutomate Samples - Github](https://github.com/AurelienClere-365/ALM-LCS-PowerAutomate)

After, you can go to your PowerAutomate account. I know that partners/customers in FinOps are not (yet ?) going very much in there, but still, I hope I will help you to discover this fantastic feature in the PowerPlatform ! And yes, normally (like me), if you had bought your F&O licences, **you have already a free account for every users, and it will be more than enough to setup some flows like this one.**

[https://flow.microsoft.com](https://flow.microsoft.com/)

Go, to “My Flows” in order to import it.

![Screenshot 2020-04-14_22-13-04-870.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586895215632-O9RJWL4RYFF4X4BJWM8V/ke17ZwdGBToddI8pDm48kJRImbYr4zzAS4oRTutlQLJ7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z5QHyNOqBUUEtDDsRWrJLTmPFWxJOCX5TaB84eCNiIPe9RsLEOh63gTfR0hFzL56M8472QQPo7IFfB0A5cnSpQL/Screenshot+2020-04-14_22-13-04-870.png)

Select my ZIP file from my Github,

![Screenshot 2020-04-14_22-17-15-902.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586895725870-M8H1XDYEE0CLJ417VZCM/ke17ZwdGBToddI8pDm48kNnXE-ImjUm64FLUZFfHAUB7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z4YTzHvnKhyp6Da-NYroOW3ZGjoBKy3azqku80C789l0n-PK7QhayQSn5KfMiCgVpEL239U_ev3Bt2OvySu8BybBdVPenL8tNXsv2QVRZ_ZvQ/Screenshot+2020-04-14_22-17-15-902.png)

Here, don’t be afraid for the 2 resources / connectors (Mail & Push mobile notification) you can setup it before importing or just map to your connectors if you had already setup for other flows before. I just used them in order to give you some example of actions, you can delete them after import process.

When you have done all done 3 imports of the sample, let’s go in 1 by 1 if you don’t mind !

* * *

## 1. PowerAutomate Sample - LCS API - Automatic refresh database

![Screenshot 2020-04-14_22-26-41-946.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586896013101-8GJY6GRAGHKMM7W6WJWD/ke17ZwdGBToddI8pDm48kBOxagEjEBCBvT6-GFvbMGcUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYy7Mythp_T-mtop-vrsUOmeInPi9iDjx9w8K4ZfjXt2dh1GJzNteJnPTF_u_nDvtX17kI02e_3MP4SBpAf3TVYXCjLISwBs8eEdxAxTptZAUg/Screenshot+2020-04-14_22-26-41-946.png)

If you go in, I will explain step-by-step in order to configure it and also give inputs why I’ve done that :)

Click on “EDIT” button.

Here is below the whole flow, without any line of code :)

![Screenshot 2020-04-14_22-27-54-950.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586896086997-MGQ9GH2Z3FWJYF15N4PU/ke17ZwdGBToddI8pDm48kJ9eH5U6XJOKZpTmjZj2RRN7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z5QHyNOqBUUEtDDsRWrJLTmtRRaKn767BRkbQRoY9JeIcBKMYkZxNnBftbXHikrf2woqcWgYLj-NnaE2zEEMsC_/Screenshot+2020-04-14_22-27-54-950.png)

So, first step, since it’s a scheduled flow in my example, I can setup when my flow will run. Here I have put every weedays at 7 p.m but sure you can edit it of course. Keep in mind, that you can of course do it by a trigger from other connector in PowerAutomate. Like maybe do it after an Azure DevOps Pipeline is finished or after a BUILD process, after also an approval by someone else in Microsoft Teams in adaptive card, by a click in a button in a PowerApps : well, I can give 150 examples so you can setup like you want !

**Just be careful also on the interval, you can setup only 3 refresh in a day for 1 target environment ! (limitation by LCS)**

![Screenshot 2020-04-14_22-33-15-144.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586896402283-Y6JPL1I2UHBYIO0OTDV4/ke17ZwdGBToddI8pDm48kNusjgAdiLNZlkLb20YFOY8UqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYxCRW4BPu10St3TBAUQYVKcPLBNw8slsAlc3KCQUSP_qYq05jGWNuL50WhzgDfdXEqMcHt_TOerJcOxwCSdBRWW/Screenshot+2020-04-14_22-33-15-144.png)

After I just create a variable to put your LCS project ID. Of course, change the value in it to reflect your current LCS project.

You can grab it directly in your LCS portal if you don’t know it

![Screenshot 2020-04-14_22-36-00-492.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586896597087-3H5DXVOFGIUFGDJFGAJK/ke17ZwdGBToddI8pDm48kAPaAM8es9YSDZgTQ8r7nLXlfiSMXz2YNBs8ylwAJx2qgRUppHe6ToX8uSOdETM-XipuQpH02DE1EkoTaghKW779xRs5veesYFcByqmynT9oN8I9Lxr8iUc_n2aYKZGfzfds731YM61VJw6aqOOyXY0/Screenshot+2020-04-14_22-36-00-492.png)


![Screenshot 2020-04-14_22-36-59-170.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586896626557-JK9DEIPARS3LSP9AL3G1/ke17ZwdGBToddI8pDm48kGYX1UIm3Rz_VO2ZrK2uKSEUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYxCRW4BPu10St3TBAUQYVKcbOGAZ6z_-ifnnakxOqBFQazLrRFr1zdoVV3soiRtJke4Ba8_i9wpXi3U8xRA88QL/Screenshot+2020-04-14_22-36-59-170.png)

After, I use the HTTP connector to perform the authentification with of course your Azure Application ID and Secret token that you have created earlier.

![Screenshot 2020-04-14_22-41-22-394.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586896899342-EB0RQ74QK8KFVXJBPDC3/ke17ZwdGBToddI8pDm48kG_NvQSHiANF7TPTaSI_HHl7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z5QHyNOqBUUEtDDsRWrJLTmzp1_Eb9xzmfqVXnlGz_LxE57M1b1KZdQxQn882182c81nc-AcUmw9lpfSqfCVVQR/Screenshot+2020-04-14_22-41-22-394.png)

You will need here only to change the values of : username, password, clientid and clientsecret.

Keep the resource and grant_type like this, as it is. Don’t change the Content-Type of the header.

One more thing : don’t use an LCS account that of course doesn’t have access and have appropriate security role. And like me, don’t use an account that has a MFA account (Multi Factor Authentification), use like an administrator/service account.

After I just use the Parse JSON feature, in order to get the object of the response. The most important thing is the property : **Access_Token**

It will be used after for every call to the LCS API, in the header, you will see :)

Keep in mind that the Token is for 1 hour of expiration !! So don’t forget to refresh your call to authenticate after 1 hour if you need to !

![Screenshot 2020-04-14_22-44-33-225.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586897083956-GD5MJSOV3RKXK1GDJGJJ/ke17ZwdGBToddI8pDm48kCpBDbFAptJJfatgsYRsoJEUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYxCRW4BPu10St3TBAUQYVKcC2kacJ0CqhLvb1Bkm6ExxyYFAuAnGr4X911ziiyyuE1TfaF2IG745r71zGtoduOI/Screenshot+2020-04-14_22-44-33-225.png)

After I have created 2 more variables : the source & target environment of your LCS environments that you want to refresh database. Of course, change by your need !

![Screenshot 2020-04-14_22-51-43-762.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586897518548-ZKBTMFVCXVYOO0MK6O3U/ke17ZwdGBToddI8pDm48kLToGKrPSJpMeKxABl-hKLkUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYxCRW4BPu10St3TBAUQYVKckIMkVtmK9itNEAsLDH5QavKgRNltTOfvPYVfDvswduDy5g7uJxbWAIXEK35pE8aZ/Screenshot+2020-04-14_22-51-43-762.png)


As a reminder, you can get the Environment ID by going in more details of LCS.

![Screenshot 2020-04-14_22-49-46-630.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586897466072-JUIHRERZE6UNHUBN2VRC/ke17ZwdGBToddI8pDm48kHWVREN8iOcIHzx5tlr8IqJZw-zPPgdn4jUwVcJE1ZvWQUxwkmyExglNqGp0IvTJZUJFbgE-7XRK3dMEBRBhUpw3LYjC_L5RLfgY2yuXmlXh89ReDWQ2Y47JFcpcYSAm8ezJ9orYS6bRLsptQlvhehQ/Screenshot+2020-04-14_22-49-46-630.png)

  
Finally I call the LCS API to refresh the database, like this :

Here you don’t have to change anything, good right ? :)

But, I want to explain the Authorization header. It’s here that we put the Access_Token of the response after authentification.

It’s like : Bearer xxxxxxxxxxxxxxxxx_veryhugemassive_token

**The space key is mandatory, so don’t forget it !**

**Of course at the end, I add it also a Parse JSON to build the object response of the API.**

![Screenshot 2020-04-14_22-52-53-666.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586897584596-K2EOR81E5E1M5YHYCUUA/ke17ZwdGBToddI8pDm48kD9zl4AkANrOeJqFEyMY3SF7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z5QHyNOqBUUEtDDsRWrJLTm7ynbqeCwAi0oNfL2wZmqFETJMfroulGoOuEBFevqkwbvfojKWdp6Pwk0PKuuvOeS/Screenshot+2020-04-14_22-52-53-666.png)

After, I added a conditional split. Of course, the purpose is to achieve a different path in the Flow, if the response is OK or not…

![Screenshot 2020-04-14_22-56-40-282.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586897815939-S3D5IQABHJMN05EQ4USY/ke17ZwdGBToddI8pDm48kPY2sMJ-GzKwn5o2beHnJ80UqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYxCRW4BPu10St3TBAUQYVKcmkrMuNj-d-pGEuEVtvmcYCGr2sWqgiuJonEbmJPoM2ZiExWWyITdhB_ChF6xESmh/Screenshot+2020-04-14_22-56-40-282.png)

In my “not success path”, I just put a Push Mobile Notification Action, but it’s just an example of course. You can change it by whatever you like. Don’t forget you have now more than 300 connectors in PowerAutomate (Microsoft and other third-party apps !)

![Screenshot 2020-04-14_22-58-57-340.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586897948036-Z3TIG622QGFMFGRI245M/ke17ZwdGBToddI8pDm48kLBLB1dLiprfPNpwZLwftbYUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYxCRW4BPu10St3TBAUQYVKchdW1C_5yliS9ClpRXzLGhYzQt9krJY4t9_9rG0IWo-MAa2XlH6IGYRO0nbD7JauW/Screenshot+2020-04-14_22-58-57-340.png)

On my “success path”, here is for me the most important feature !

First step here, I wait for 1 hour, magic no ? but why ? Well because, we don’t have to call LCS API to fetch the status of the current activity of the database refresh until more than 1 hour. Like me, it takes 1hour30min and sometimes maybe 2 hours to finish all the refresh. But you change it of course by adding just 30 minutes of wait if you had experience faster refresh on your side ! It’s also for me a good example to show you that we can put Expression without to be a massive developper !

![Screenshot 2020-04-14_23-00-18-825.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586898030953-XB2Q2JXULPGBFABQNDNA/ke17ZwdGBToddI8pDm48kPywePQIGQ6MGw-HhOE94B0UqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYwL8IeDg6_3B-BRuF4nNrNcQkVuAT7tdErd0wQFEGFSnMveftintnGPb9KKrBtKm8rYmhUdw8UivY3LGcG5doeU5ykmURjYCpOMCVp1Dd1dkg/Screenshot+2020-04-14_23-00-18-825.png)

  
After I’ve done a Do Until feature, in order to call every 5 minutes the LCS API to fetch the status. In order to after doing some actions in the same flow.

The end of my “while'“ loop is until we’ve got a Status Operation than is not equal to : In Progress. **Just be careful of the Change Limits here, you have to increase it to 300 like me**, because by default it’s 60 : that’s say that we will only do this part for 60 minutes. But I already do that for you :)

Update (17/04/20) : Just found that you can also increase the timeout of the Do Until statement. By default it’s PT1H , you can change by PT2H for 2 hours or more if you need.

![Screenshot 2020-04-14_23-04-57-449.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586898355106-A256FF32XMO5MUUCEQX9/ke17ZwdGBToddI8pDm48kCYRzNJyjW7_yn5qib_bEiIUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYxCRW4BPu10St3TBAUQYVKc-vmkWlwiNI1W5qMgJSzaXm-MV9obnrfVCMgtxxEIjfj3gRYTso5VTy9GH_TnkTet/Screenshot+2020-04-14_23-04-57-449.png)

After, I just fetch the status of the Operation/Activity of the database refresh to know if it’s finished :) Parsing JSON again of course, every time in fact so I hope you are now familiar with that.

![Screenshot 2020-04-14_23-05-29-692.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586898535078-LWGB6KA2PDQDR2FWNTS3/ke17ZwdGBToddI8pDm48kE044ww_KRI14uDl4Kh6OdB7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z5QHyNOqBUUEtDDsRWrJLTmrP7WItXxdD8oQEwWz4rNcfx3qYhZCnpzNskxJy34L14Q5dvIzbcV3w4obtUcqdlT/Screenshot+2020-04-14_23-05-29-692.png)

And now, when it’s ended, why I’ve done that ? Well because on my side (and I think you also), you have some “Post-Refresh” statement - script or SQL script to achieve. As you know, after a refresh database, you have all BatchJobs On Hold, only 1 user access activated, and you want maybe to change some parameter to reflect the change and of course don’t activate the Email distributor batch to sent email to a customer from an UAT Instance… believe me I’ve seen that many times !

So that’s why I’ve put this last step here and will explain more deeper with just a small part of X++ code for AX Technical guys.

![Screenshot 2020-04-14_23-12-48-036.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586898779648-OH17KB46ZOYYG7S7OW3R/ke17ZwdGBToddI8pDm48kJB4Zm0fT1_DkfR5OLfMPH8UqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYxCRW4BPu10St3TBAUQYVKcOEutgjccf2d7txDUONr3n33jGwaC4Ywnq6szRl0-NPGqS_oy1YME7g4agPvGgeEl/Screenshot+2020-04-14_23-12-48-036.png)

In fact, it was not possible for me to give you a sample of the Dyn365 F&O connector to share it (without giving my credentials) directly in this sample, so you will need to do it yourself for this part.

So, of course you will erase this part of sending email after completion, it’s irrelevant. You already receive an email from LCS when the refresh is done.

First, you can of course add other actions if you want to, like I’ve said before you have 300 connectors so you can put something else.

But for me, you will change by that !! The Dynamics 365 FinOps connector in order to call Action / in fact it’s like a job in AX. It refers to the methods applied to a data entity.

Create a new step like that by choosing the right connector. You will notice that you have multiple actions. Here you can see that for other Flow/PowerAutomate you can achieve more : by creating, updating, retrieving records etc…. so for other use cases, you can have a look !

![Screenshot 2020-04-14_23-18-07-784.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586899101295-GX48X9FYGOGINUBPQJTQ/ke17ZwdGBToddI8pDm48kDMeEXQHkHFHl7JtcGDXsNx7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z5QHyNOqBUUEtDDsRWrJLTmbTGb-uIaJrW1hb62VKR-WQUjy6RmmYdFuUMcRoRKRFFowquYUhiCaWVmbLRXKHR-/Screenshot+2020-04-14_23-18-07-784.png)

Here, I will pick “Execute Action” :

![Screenshot 2020-04-14_23-20-21-574.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586899343567-4EOHUSR8XEGVZIVNZAE1/ke17ZwdGBToddI8pDm48kCq1VxWbYUEoQo0GSj5Rz1AUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYxCRW4BPu10St3TBAUQYVKcYxkYOprD1odUqsid1tzl-AC9CaVwmwSWH_UxWDxUr9dKfAl_2WCPvTog9w3WXvur/Screenshot+2020-04-14_23-20-21-574.png)

But, like me, my question was : “What the hell are coming from these actions ???”

See the documentation of the connector F&O :

[Learn more](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/data-entities/fin-ops-connector?WT.mc_id=BA-MVP-5003744)

Like I said, it’s coming from Methods in the Data Entities in F&O. So like I’ve done below, you can create a custom one, and create method that in fact will do of course your post-refresh database script. Like me, it was done for reactivate all users, batchjobs. But sure, you can do it depending on your needs !

*   **Just as a reminder, don’t forget to use the connector F&O with the Environment Manager that have access after a database refresh !**
    

So just create a new Data entity, that refer to whatever table coming from AX like that :

![Screenshot 2020-04-14_23-24-55-272.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586899557976-UWA7W5M2AA86W06WCGPH/ke17ZwdGBToddI8pDm48kMAfXyi2hWkRKYfWL1L-9ytZw-zPPgdn4jUwVcJE1ZvWQUxwkmyExglNqGp0IvTJZUJFbgE-7XRK3dMEBRBhUpyECULE1HnyqH3pLMx6RmkrApnRZUfQqDNljbc64M4Bn_3Crv_a795OMg9fcLpKYRM/Screenshot+2020-04-14_23-24-55-272.png)

Create your methods, that refer to **SysODataActionAttribute**.

Here I don’t give a response (void) but you can of course also put some object if you need to get back to PowerAutomate flow.

![Screenshot 2020-04-14_23-25-11-005.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586899590906-Q3WJBHAQ191K0HLWSR8J/ke17ZwdGBToddI8pDm48kHjXoLbmJg50zveD-Fp2EMF7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z5QHyNOqBUUEtDDsRWrJLTmKhfNlgrI9RoL2yig5n9s2SMrm1ZgV1pWNwV5Cw9AV13ZXZVbGHRdgCaGfh2jpVBk/Screenshot+2020-04-14_23-25-11-005.png)

Here we are, finished for this first sample.

* * *

## 2. LCS API - Export database

You already know that we can export database also on the LCS Portal by manual action on a Sandbox instance. Of course, it can be good to do it like just after our previous sample because we have done a flow (DataALM) to refresh the production to an UAT instance. If you want to get the .bacpac (backup of SQL Azure) in order to deploy it (implied your production database) to a DEV environment. Maybe also because you want to archive it to a safe repository. So yes, the LCS API has already a method for that, that’s why I made this sample for you.

I assume that you have already download it from my GitHub Account before and already import it like the previous one.

Go in this flow.

I will not explain again this part, because it’s exactly the same as the Database Refresh.

![Screenshot 2020-04-14_23-35-58-495.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586900170795-NP9T0CRE1FLL0OPRQS86/ke17ZwdGBToddI8pDm48kHRr2a4pD_4mXM92OO0vSzcUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYxCRW4BPu10St3TBAUQYVKcuaKRxl3sM-POo7-nZbOeNs60ZpBKv-1EWqCm9YTvHlLvujTjex0eTdJMDS4tiJQ4/Screenshot+2020-04-14_23-35-58-495.png)

After, I made 2 variables : the backupName wanted and also the environmentId that you want to export the database.

![Screenshot 2020-04-14_23-36-59-889.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586900276735-QW1D3DHITWQCKZCXWD9C/ke17ZwdGBToddI8pDm48kAUtUUd_CxcGrA3MDWseIqgUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYxCRW4BPu10St3TBAUQYVKcslFux2q53GerjDsuC1EM4rii86fyK78Q1uJ8-19Wi0CxFscXz3tvN1GriILYpFuk/Screenshot+2020-04-14_23-36-59-889.png)

After, I call the LCS API and grab the response to a JSON object.

![Screenshot 2020-04-14_23-38-25-516.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586900358683-6ENVHP1P2UDZ71CFE4IH/ke17ZwdGBToddI8pDm48kCoa3UrihdvfcTPT1-Da-TF7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z5QHyNOqBUUEtDDsRWrJLTmKqdaCfszdx2FwqNe3GVbROKyBll-kk6Z1eSOg1MHc7GzIWbSL8e6DJWh5JI4dB2F/Screenshot+2020-04-14_23-38-25-516.png)

Finally the conditional split, you can change like before by whatever you want.

![Screenshot 2020-04-14_23-38-38-379.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586900451630-NZW6Z8HTBL75LY94Q3J2/ke17ZwdGBToddI8pDm48kNok3zLgiIkhxH093ejvkKUUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYwL8IeDg6_3B-BRuF4nNrNcQkVuAT7tdErd0wQFEGFSnNGZUh7LabIOf3dCF5BOhYYfjcwaWGET3u0KX1EUtmfjb56nQZ7fqnCLzSsOdTaFqQ/Screenshot+2020-04-14_23-38-38-379.png)

* * *

## 3. Retrieve list of database backup
---------------------------------------------------------------------

Here, we will have the possibility to retrieve the list of the whole backup list in your LCS Asset Library. As you know, it’s the default storage, where all database backup are coming.

![Screenshot 2020-04-14_23-42-44-006.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586900576510-H6FM7FB30M471W4QGCV6/ke17ZwdGBToddI8pDm48kMkGPR0Z7Bj3qMxILzmR5fR7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z5QHyNOqBUUEtDDsRWrJLTmGQO1q8NTa-d1SLBPBcOGJyY16eUHk_AZbogR_cUcQxJDalN1ZDgjLQLaCwK7WlaR/Screenshot+2020-04-14_23-42-44-006.png)

At the end I’ve done a For Each feature, good way to show you this point !

![Screenshot 2020-04-14_23-44-54-577.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586900719105-W7ZID8VJ3356J3PFZE0Z/ke17ZwdGBToddI8pDm48kE_1RTRtECuLZI3pALqR81l7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z5QHyNOqBUUEtDDsRWrJLTmvAQioOWEzfze6FPGriiNu5Iq1LLzxmcZ5tU2_9DkpKCvMvy3mnraVV40aNpvxzlB/Screenshot+2020-04-14_23-44-54-577.png)

Well… sad news : true. Because I try to add also an another HTTP Connector for the FileLocation (it’s the HTTP location where you can download your backup) but the limitation is for 100MB so you will get like me an error. My aim was after to grab the fileContent to a OneDrive, Google Drive, Sharepoint or Azure Blob Storage, but yes… at this time we can’t.

The only way that I have for you, is to do it by a more complicated way. You can create a powershell script in your own network / share folder (onPremise). You can after download the Data Gateway for PowerAutomate in order to call your script, passing through the FileLocation and your Powershell will download it on your onPremise folder. Yes I didn’t talk about that, but yes we can achieve a connection from PowerAutomate (SaaS - Cloud Only) to connect on an onPremise API - Service.

![on-premises-data-gateway.png](https://images.squarespace-cdn.com/content/v1/5dee13c4b2178b0eae4710df/1586901630565-2WDSC40UAKXVOBX7JEOI/ke17ZwdGBToddI8pDm48kFcxCCt_xNkZ48mab5WAgUFZw-zPPgdn4jUwVcJE1ZvWQUxwkmyExglNqGp0IvTJZUJFbgE-7XRK3dMEBRBhUpwR76lFL8uFTtqw-iaosXIJV87SnD-zsHoIhNOnAkeD45UEJ_72GcD6XjtaCQ5qQlc/on-premises-data-gateway.png)

[PowerAutomate - Data Gateway onPremise](https://docs.microsoft.com/en-us/power-automate/gateway-reference?WT.mc_id=BA-MVP-5003744)

[PowerAutomate - Powershell onPremise](https://sergeluca.wordpress.com/2017/12/01/upload-and-run-a-remote-powershell-script-from-microsoft-flow/)

* * *

To conclude : I hope you like it :) Besides that, feel free to contact me, comment, do feedbacks etc…

You can see easily that PowerAutomate in part of the PowerPlatform is really a huge feature that will help us in a lot of flows for F&O project.

Because despite this technical part of DataALM, like I’ve done for other articles in this blog, we can achieve a lot more of use cases now ! Business cases , Business Events etc…

See you ! Stay tuned. Add me on social networks :)
