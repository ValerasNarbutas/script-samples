

# Send a delayed message

## Summary

This script shows how to send a delayed message with the Microsoft Graph PowerShell SDK. 

The [mail API](https://learn.microsoft.com/graph/api/resources/mail-api-overview) in the Microsoft Graph doesn't expose any properties to defer sending a message.

### Set deferred send time
Fortunately, the Graph API provides [the extended properties](https://learn.microsoft.com/graph/api/resources/extended-properties-overview) REST API if you need to access custom data for Outlook MAPI properties that are not already exposed in the Microsoft Graph API.

Outlook MAPI provides the canonical property [PidTagDeferredSendTime](https://learn.microsoft.com/office/client-developer/outlook/mapi/pidtagdeferredsendtime-canonical-property).

`PidTagDeferredSendTime` indicates a time when a client would like to defer sending a message.

|Property|Value|
|-|-|
|Associated properties| PR_DEFERRED_SEND_TIME|
|Identifier|0x3FEF|
|Data type|PT_SYSTIME|

When sending a message, provide a `singleValueLegacyExtendedProperty` object in the `singleValueExtendedProperties` collection property of the message resource instance.

`singleValueLegacyExtendedProperty` has properties `id` and `value`. 

`id` must follow the format `"{graph_type} {proptag}"`.

`graph_type` for MAPI data type `PT_SYSTIME` is `SystemTime`, and `proptag` is `0x03FEF`.

The date and time value must be in ISO 8601 format.

![Example Screenshot](assets/example.png)

# [Microsoft Graph PowerShell](#tab/graphps)

The script shows how to defer message delivery by specific minutes. 

Input parameters:
- `$SenderId`: Sender id, mail or a UPN
- `$RecipientEmail`: The recipient of the message
- `$DeliveryDelayMinutes`: Delivery delay in minutes
- `$Subject`: The subject of the message
- `$Body`: The body of the message

```powershell

[cmdletbinding()]
param(
    [parameter(Mandatory)]
    [string] $SenderId,
    [parameter(Mandatory)]
    [string] $RecipientEmail,
    [parameter(Mandatory)]
    [int] $DeliveryDelayMinutes,
    [parameter(Mandatory)]
    [string] $Subject,
    [parameter(Mandatory)]
    [string] $Body
)

function Send-MgUserDelayedMail {
    try {

    Connect-MgGraph -Scopes "Mail.Send"

    Import-Module Microsoft.Graph.Users.Actions

    $DeliveryTime=(Get-Date).AddMinutes($DeliveryDelayMinutes).ToUniversalTime().ToString("o")

    $Params = @{
        message = @{
            subject = $Subject
            body = @{
                contentType = "HTML"
                content = $Body
            }
            toRecipients = @(
                @{
                    emailAddress = @{
                        address = $RecipientEmail
                    }
                }
            )
            singleValueExtendedProperties = @(
                @{
                    id = "SystemTime 0x3FEF"
                    value = $DeliveryTime
                }
            )
        }
    }


    Send-MgUserMail -UserId $SenderId -BodyParameter $Params
    Write-Host "The email will be delivered at $DeliveryTime"
    }
    catch {
        Write-Host 'Failed to send delayed message'
        Write-Host $_
    }
    finally {
        Disconnect-MgGraph
    }
}

Send-MgUserDelayedMail

```
[!INCLUDE [More about Microsoft Graph PowerShell SDK](../../docfx/includes/MORE-GRAPHSDK.md)]
***


## Contributors

| Author(s) |
|-----------|
| [Martin Macháček](https://github.com/MartinM85) |

[!INCLUDE [DISCLAIMER](../../docfx/includes/DISCLAIMER.md)]
<img src="https://m365-visitor-stats.azurewebsites.net/script-samples/scripts/graph-delay-message-delivery" aria-hidden="true" />
