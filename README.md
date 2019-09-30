# Sending Azure Service Health Alerts to Teams Channels Using Azure Functions

The ASHWebhookToMessageCardFn.ps1 file contains code to use in a Powershell-based [Azure function](https://azure.microsoft.com/en-us/services/functions/) triggered by an incoming webhook (http trigger).  *Azure Functions with Powershell runtimes are currently in Preview.*

This code parses the payload of the incoming webhook and creates a [MessageCard](https://docs.microsoft.com/en-us/outlook/actionable-messages/message-card-reference) which is then sent as a webhook to a URI specified in an environment variable for the function.

While this code was built for use with Office365 Teams, the messageCard format is used in other Microsoft tools such as Outlook so this code could probably be adapted to other purposes with a bit of work.

**Future plans:**  In the future this should be updated to use the newer and more flexible [Adaptive Card](https://docs.microsoft.com/en-us/outlook/actionable-messages/adaptive-card) format.

## How to use this (simplified)

1. Create a Powershell function in Azure which uses a http (incoming webhook) trigger.  **[This page](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-function-powershell)** is a good walkthrough of the process using [Visual Studio Code](https://code.visualstudio.com/).  This will prepopulate the function with some skeleton Powershell code in a file named `run.ps1`.
2. Replace _all_ code in the `run.ps1` file with the contents of the ps1 file in this repository.  No changes should be needed.
3. Create an **[Azure Service Health alert](https://docs.microsoft.com/en-us/azure/service-health/alerts-activity-log-service-notifications)** with an **[Action Group](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/action-groups)** that sends a webhook to the trigger URI for the Azure function you created. ***The service health alert must be configured to use the [common alert schema](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/alerts-common-schema) or you will get parse errors from the function when it runs.*** 
4. Get the trigger URI for your new function by clicking on "</> Get Function Url" on the screen which shows the function's code.  The URL should look like `https://<yourfunctionname>.azurewebsites.net/api/...`
5. In Teams, **[create a Webhook Connector](https://docs.microsoft.com/en-us/microsoftteams/platform/concepts/connectors/connectors-using#setting-up-a-custom-incoming-webhook)** for the channel that should receive the alert notifications and make a note of the webhook URI.
6. Back in the Azure portal, create an application setting for your function named **webhookuri**.  This is done using the **[Application settings](https://docs.microsoft.com/en-us/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings)** screen for the function with the **New application setting** button.  
Paste in the URI for the Teams channel connector that you created in the previous step as the value of the application setting.  Application settings are visible to the function as environment variables.
7. Use a sample service health alert payload like the one at **[this link](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/activity-log-alerts-webhook#servicehealth)** to trigger your function by calling the URI using curl or invoke-restmethod.  This repository has a copy of the sample payload from this page in the file `SampleServiceHealthAlertWebhookPayload.json`.  

**Example:**  ```Invoke-RestMethod -Method POST -uri $YourFunctionsURI -body (gc SamplePayload.json)```

A message should appear in your Teams channel that looks like the screenshot at the bottom of this README.  If the screenshot looks good then the function is working.

8. Wait for an Azure service health alert to trigger your function and check out the result!

The color of the bar at the top of the messageCard varies by the type of alert.  In general, red indicates an urgent active issue, yellow indicates that action is needed, green indicates planned maintenance and blue indicates a message related to resolved or inactive alert such as a RCA posting.  The MessageCard schema is fairly flexible and makes it easy to do things like add an icon to the card as well.

![MessageCard Screenshot](https://github.com/KenHoover/AzureSHStuff/blob/master/SampleHealthAlertCard.PNG?raw=true "Sample MessageCard Output")

