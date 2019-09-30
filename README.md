# Azure Function: Sending Azure Service Health Alerts to Teams Channels

The ASHWebhookToMessageCardFn.ps1 file contains code to use in a Powershell-based Azure function triggered by an incoming webhook (http trigger).  

This function parses the payload of the webhook and converts it to a [MessageCard](https://docs.microsoft.com/en-us/outlook/actionable-messages/message-card-reference)  which is then sent as a webhook to a URI specified in an environment variable for the function.

While this code was built for use with Office365 Teams, the messageCard format is used in other places as well so this code could probably be adapted to other purposes with a bit of work.

**Future plans:**  In the future this should be updated to use the newer and more flexible [Adaptive Card](https://docs.microsoft.com/en-us/outlook/actionable-messages/adaptive-card) format.

## How to use this (simplified)

1. Create a Powershell function in Azure which uses a http (incoming webhook) trigger.  **[This page](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-function-powershell)** is a good walkthrough of the process using [Visual Studio Code](https://code.visualstudio.com/).  This will prepopulate the function with some skeleton Powershell code in a file named `run.ps1`.
2. Replace the code in the `run.ps1` file with the contents of the ps1 file in this repository.  No changes should be needed.
3. Create an **[Azure Service Health alert](https://docs.microsoft.com/en-us/azure/service-health/alerts-activity-log-service-notifications)** with an **[Action Group](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/action-groups)** that sends a webhook to the trigger URI for the Azure function you created. ***The service health alert must be configured to use the [common alert schema](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/alerts-common-schema).*** You can get the trigger URI for your new function by clicking on "</> Get Function Url" on the function's screen.  It should look like `https://<yourfunctionname>.azurewebsites.net/api/...`
4. In Teams, **[create a Webhook Connector](https://docs.microsoft.com/en-us/microsoftteams/platform/concepts/connectors/connectors-using#setting-up-a-custom-incoming-webhook)** for the channel that should receive the alert notifications and make a note of the webhook URI.
5. Create an environment variable for your function named **webhookuri**.  This is done using the **[Application settings](https://docs.microsoft.com/en-us/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings)** screen for the function with the **New application setting** button.  
Paste in the URI for the Teams channel connector that you created in the previous step as the value of the application setting.
6. Use a sample service health alert payload like the one at **[this link](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/activity-log-alerts-webhook#servicehealth)** to trigger your function by calling the URI using curl or invoke-restmethod.  This repository has a copy of the sample payload from this page in the file `SampleServiceHealthAlertWebhookPayload.json`.  A message should appear in your Teams channel that looks like the screenshot below.  If you get the screenshot then the function is working and should trigger correctly when a service health alert using the action group you created is fired.

The color of the bar at the top of the messageCard varies by the type of alert.  In general, red indicates an urgent active issue, yellow indicates that action is needed, green indicates planned maintenance and blue indicates a message related to resolved or inactive alert such as a RCA posting.

![MessageCard Screenshot](https://github.com/KenHoover/AzureSHStuff/blob/master/SampleHealthAlertCard.PNG?raw=true "Sample MessageCard Output")

