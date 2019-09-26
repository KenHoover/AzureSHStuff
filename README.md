# Azure Function: Sending Azure Srevice Health Alerts to Teams Channels

The ASHWebhookToMessageCardFn.ps1 file contains code to use in a Powershell-based Azure function triggered by an incoming webhook (http trigger).  

This function parses the payload of the webhook and converts it to a [MessageCard](https://docs.microsoft.com/en-us/outlook/actionable-messages/message-card-reference)  which is then sent as a webhook to a URI specified in an environment variable for the function.

While the function was built for use with Office365 Teams, the messageCard format is used in other places as well so this could probably be adapted to other purposes.

In the future this should be updated to use the newer and more flexible [Adaptive Card](https://docs.microsoft.com/en-us/outlook/actionable-messages/adaptive-card) format.


