---
title: Datadog-Webhooks Integration
integration_title: Webhooks
kind: integration
newhlevel: true
---

# Overview

With Webhooks you'll be able to:

* Connect to your services.
* Alert your services when a metric alert is triggered.


# Configuration

Go to the [webhook integration tile](https://app.datadoghq.com/account/settings#integrations/webhooks) and enter the URL and name of the webhook you want to use

# Usage

To use your webhook, add **@webhook-*name_of_the_webhook*** in the text of the metric alert you want to trigger the webhook. It will trigger a POST request to the URL you set with the following content in JSON format.

You can also specify your own payload in order to add your own custom fields to the request. Check the **Use Custom Payload** checkbox and specify your own custom payload, using the following variables. If you want your payload to be URL-encoded, check the **Encode as form** payload and specify your payload in a json format.

|Variable|Meaning|
|-----|-----|
|$ID | ID of the event *(ex: 1234567)*|
|$EVENT_TITLE| Title of the event *(ex: \[Triggered] \[Memory Alert])*|
|$EVENT_MSG| Text of the event *(ex: @webhook-url Sending to the webhook)*|
|$EVENT_TYPE| Type of the event *(values: `metric_alert_monitor`, `event_alert`, or `service_check`)*|
|$LAST_UPDATED| Date when the event was last updated .|
|$DATE| Date *(epoch)* where the event happened *(ex: 1406662672000)*|
|$AGGREG_KEY| ID to aggregate events belonging together *(ex: 9bd4ac313a4d1e8fae2482df7b77628)*|
|$ORG_ID| ID of your organization *(ex: 11023)*|
|$ORG_NAME| Name of your organization *(ex: Datadog)*|
|$USER| User posting the event that triggered the webhook *(ex: rudy)*|
|$SNAPSHOT| Url of the image if the event contains a snapshot *(ex: https://url.to.snpashot.com/)*|
|$LINK| Url of the event *(ex: https://app.datadoghq.com/event/jump_to?event_id=123456)*|
|$PRIORITY| Priority of the event *(values: `normal` or `low`)*|
|$TAGS| Comma-separated list of the event tags *(ex: `monitor, name:myService, role:computing-node`)*|
|$ALERT_ID| ID of alert *(ex: 1234)*|
|$ALERT_TITLE| Title of the alert|
|$ALERT_METRIC| Name of the metric if it's an alert *(ex: `system.load.1`)*|
|$ALERT_SCOPE| Comma-separated list of tags triggering the alert *(ex: `availability-zone:us-east-1a, role:computing-node`)*|
|$ALERT_QUERY| Query of the monitor that triggered the webhook|
|$ALERT_STATUS| Summary of the alert status *(ex: system.load.1 over host:my-host was > 0 at least once during the last 1m)*|
|$ALERT_TRANSITION| Type of alert notification *(values: `Triggered` or `Recovered`)*|
{:.table}

If you want to post your webhooks to a service requiring authentication, you can Basic HTTP authentication my modifing your URL from `https://my.service.com` to `https://username:password@my.service.com`.

## Examples

### Sending SMS through Twilio

Use as URL:
`https://{Your-Account-id}:{Your-Auth-Token}@api.twilio.com/2010-04-01/Accounts/{Your-Account-id}/Messages.json`
and as payload

    {
        "To":"+1347XXXXXXX",
        "From":"+1347XXXXXX",
        "Body":"$EVENT_TITLE \n Related Graph: $SNAPSHOT"
    }
{:.language-json}


replacing `To` with your phone number and `From` with the one twilio attributed to you. Check the **Encode as form** checkbox.

### Creating an issue in Jira

Use as URL:
`https://{Your-Jira-Username}:{Your-Jira-Password}@{Your-Domain}.atlassian.net/rest/api/2/issue`
and as payload

    {
        "fields": {
            "project": {
                "key": "YOUR-PROJECT-KEY"
                },
            "issuetype": {
                "name": "Task"
            },
            "description": "There's an issue. See the graph: $SNAPSHOT and event: $LINK",
            "summary": "$EVENT_TITLE"
        }
    }
{:.language-json}
Don't check the "Encode as form" checkbox.
