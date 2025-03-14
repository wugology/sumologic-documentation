---
id: 1password-source
title: 1Password Source
sidebar_label: 1Password
description: 1Password source
---

import useBaseUrl from '@docusaurus/useBaseUrl';


The 1Password Source provides a secure endpoint to receive Sign-in Attempts and Item Usage from the [1Password Event API](https://support.1password.com/events-api-reference/). It securely stores the required authentication, scheduling, and state tracking information.

The 1Password Source ingests:

* [Sign-in Attempts](https://support.1password.com/events-api-reference/#sign-in-attempts)
* [Item Usage](https://support.1password.com/events-api-reference/#item-usage)


#### Rules

* JSON is the only supported log format
* Data is collected in five minute intervals.


#### Authentication

You need a 1Password API token and your customer specific 1Password domain, for example `events.1password.com.`

To generate a 1Password API token follow these steps:


1. [Sign in](https://start.1password.com/signin) to your 1Password account and click [Integrations](https://my.1password.com/integrations/active) in the sidebar.
2. Choose the Events Reporting integration where you want to issue a token and click **Add a token**.
3. Enter a name for the bearer token and choose when it will expire. Select or deselect the event types the token has access to, then click **Issue Token**.
4. Click **Save** in 1Password and choose which vault to save your token to. Then click **View Integration Details**.


#### States

A 1Password Source tracks errors, reports its health, and start-up progress. You’re informed, in real-time, if the Source is having trouble connecting, if there's an error requiring user action, or if it is healthy and collecting by utilizing [Health Events](https://help.sumologic.com/manage/Health_Events).

A 1Password Source goes through the following states when created:

1. **Pending**: Once the Source is submitted it is validated, stored, and placed in a **Pending** state.
2. **Started**: A collection task is created on the Hosted Collector.
3. **Initialized**: The task configuration is complete in Sumo Logic.
4. **Authenticated**: The Source successfully authenticated with 1Password.
5. **Collecting**: The Source is actively collecting data from 1Password.

If the Source has any issues during any one of these states it is placed in an **Error** state.

When you delete the Source it is placed in a **Stopping** state, when it has successfully stopped it is deleted from your Hosted Collector.


On the Collection page, the [Health](https://help.sumologic.com/manage/Health_Events#Collection_page) and Status for Sources is displayed. Use [Health Events](https://help.sumologic.com/manage/Health_Events) to investigate issues with collection. You can click the text in the Health column, such as **Error**, to open the issue in Health Events to investigate.


Hover your mouse over the status icon to view a tooltip with details on the detected issue.


#### Create a 1Password Source




When you create a 1Password Source, you add it to a Hosted Collector. Before creating the Source, identify the Hosted Collector you want to use or create a new Hosted Collector. For instructions, see [Create a Hosted Collector](https://help.sumologic.com/03Send-Data/Hosted-Collectors#Create_a_Hosted_Collector).

To configure a 1Password Source:

1. In Sumo Logic, select** Manage Data > Collection > Collection**.
2. On the Collectors page, click **Add Source** next to a Hosted** **Collector.
3. Select **1Password**.
4. Enter a **Name **for the Source. The **description** is optional.
5. (Optional) For **Source Category**, enter any string to tag the output collected from the Source. Category [metadata](https://help.sumologic.com/05Search/Get-Started-with-Search/Search-Basics/Built-in-Metadata) is stored in a searchable field called `_sourceCategory`.
6. **Forward to SIEM**. Check the checkbox to forward your data to [Cloud SIEM Enterprise](https://help.sumologic.com/Cloud_SIEM_Enterprise).

When configured with the **Forward to SIEM** option the following metadata fields are set:



<table>
  <tr>
   <td>
Field Name
   </td>
   <td>API
   </td>
   <td>Value
   </td>
  </tr>
  <tr>
   <td>_siemVendor
   </td>
   <td>Sign-in, Item
   </td>
   <td>1Password
   </td>
  </tr>
  <tr>
   <td>_siemProduct
   </td>
   <td>Sign-in, Item
   </td>
   <td>1Password
   </td>
  </tr>
  <tr>
   <td>_siemFormat
   </td>
   <td>Sign-in, Item
   </td>
   <td>JSON
   </td>
  </tr>
  <tr>
   <td>_siemEventID
   </td>
   <td>Sign-in
   </td>
   <td><code>signin-&#123;&#123;category&#125;&#125;</code>
   </td>
  </tr>
  <tr>
   <td>_siemEventID
   </td>
   <td>Item
   </td>
   <td><code>item_usage-&#123;&#123;action&#125;&#125;</code>
   </td>
  </tr>
</table>

1. (Optional) **Fields**. Click the **+Add** link to add custom log metadata [Fields](https://help.sumologic.com/manage/fields).
   * Define the fields you want to associate, each field needs a name (key) and value.
      * ![green check circle.png](/img/reuse/green-check-circle.png) A green circle with a check mark is shown when the field exists and is enabled in the Fields table schema.
      * ![orange exclamation point.png](/img/reuse/orange-exclamation-point.png) An orange triangle with an exclamation point is shown when the field doesn't exist, or is disabled, in the Fields table schema. In this case, an option to automatically add or enable the nonexistent fields to the Fields table schema is provided. If a field is sent to Sumo that does not exist in the Fields schema or is disabled it is ignored, known as dropped.
2. **Base URL**. Provide your 1Password customer-specific domain, for example `events.1password.com`.
3. **API Token**. Enter the token you got from creating your 1Password API token in the [Authentication section](https://help.sumologic.com/03Send-Data/Sources/02Sources-for-Hosted-Collectors/Cloud-to-Cloud_Integration_Framework/1Password_Source#Authentication) above.
4. **Supported APIs to collect**. Select one or more of the available APIs, **Item Usage** and **Sign-in Attempts**.
5. When you are finished configuring the Source click **Submit**.


#### Error types

When Sumo Logic detects an issue it is tracked by [Health Events](https://help.sumologic.com/manage/Health_Events). The following table shows the three possible error types, the reason the error would occur, if the Source attempts to retry, and the name of the event log in the Health Event Index.

<table>
  <tr>
   <td>Type
   </td>
   <td>Reason
   </td>
   <td>Retries
   </td>
   <td>Retry Behavior
   </td>
   <td>Health Event Name
   </td>
  </tr>
  <tr>
   <td>ThirdPartyConfig
   </td>
   <td>Normally due to an invalid configuration. You'll need to review your Source configuration and make an update.
   </td>
   <td>No retries are attempted until the Source is updated.
   </td>
   <td>Not applicable
   </td>
   <td>ThirdPartyConfigError
   </td>
  </tr>
  <tr>
   <td>ThirdPartyGeneric
   </td>
   <td>Normally due to an error communicating with the third party service APIs.
   </td>
   <td>Yes
   </td>
   <td>The Source will retry for up to 90 minutes, after which it quits.
   </td>
   <td>ThirdPartyGenericError
   </td>
  </tr>
  <tr>
   <td>FirstPartyGeneric
   </td>
   <td>Normally due to an error communicating with the internal Sumo Logic APIs.
   </td>
   <td>Yes
   </td>
   <td>The Source will retry for up to 90 minutes, after which it quits.
   </td>
   <td>FirstPartyGenericError
   </td>
  </tr>
</table>


#### JSON configuration

Sources can be configured using UTF-8 encoded JSON files with the [Collector Management API](https://help.sumologic.com/api/collectors). See [how to use JSON to configure Sources](https://help.sumologic.com/03Send-Data/Sources/03Use-JSON-to-Configure-Sources) for details.

<table>
  <tr>
   <td><strong>Parameter</strong>
   </td>
   <td><strong>Type</strong>
   </td>
   <td><strong>Required?</strong>
   </td>
   <td><strong>Description</strong>
   </td>
   <td><strong>Access</strong>
   </td>
  </tr>
  <tr>
   <td>config
   </td>
   <td>JSON Object
   </td>
   <td>Yes
   </td>
   <td>Contains the<a href="https://help.sumologic.com/03Send-Data/Sources/02Sources-for-Hosted-Collectors/Cloud-to-Cloud_Integration_Framework/Duo_Source#configParameters"> configuration parameters</a> for the Source.
   </td>
   <td>
   </td>
  </tr>
  <tr>
   <td>schemaRef
   </td>
   <td>JSON Object
   </td>
   <td>Yes
   </td>
   <td>Use <code>&#123;"type":"1Password"&#125;</code> for a 1Password Source.
   </td>
   <td>not modifiable
   </td>
  </tr>
  <tr>
   <td>sourceType
   </td>
   <td>String
   </td>
   <td>Yes
   </td>
   <td>Use <code>Universal</code> for a 1Password Source.
   </td>
   <td>not modifiable
   </td>
  </tr>
</table>

The following table shows the **config** parameters for a 1Password Source.

<table>
  <tr>
   <td><strong>Parameter</strong>
   </td>
   <td><strong>Type</strong>
   </td>
   <td><strong>Required</strong>
   </td>
   <td><strong>Default</strong>
   </td>
   <td><strong>Description</strong>
   </td>
   <td><strong>Access</strong>
   </td>
  </tr>
  <tr>
   <td>name
   </td>
   <td>String
   </td>
   <td>Yes
   </td>
   <td>
   </td>
   <td>Type a desired name of the Source. The name must be unique per Collector. This value is assigned to the <a href="https://help.sumologic.com/05Search/Get-Started-with-Search/Search-Basics/Built-in-Metadata">metadata</a> field <code>_source</code>.
   </td>
   <td>modifiable
   </td>
  </tr>
  <tr>
   <td>description
   </td>
   <td>String
   </td>
   <td>No
   </td>
   <td>null
   </td>
   <td>Type a description of the Source.
   </td>
   <td>modifiable
   </td>
  </tr>
  <tr>
   <td>category
   </td>
   <td>String
   </td>
   <td>No
   </td>
   <td>null
   </td>
   <td>Type a category of the source. This value is assigned to the <a href="https://help.sumologic.com/05Search/Get-Started-with-Search/Search-Basics/Built-in-Metadata">metadata</a> field <code>_sourceCategory</code>. See <a href="https://help.sumologic.com/03Send-Data/01-Design-Your-Deployment/Best-Practices%3A-Good-Source-Category%2C-Bad-Source-Category">best practices</a> for details.
   </td>
   <td>modifiable
   </td>
  </tr>
  <tr>
   <td>fields
   </td>
   <td>JSON Object
   </td>
   <td>No
   </td>
   <td>
   </td>
   <td>JSON map of key-value fields (metadata) to apply to the Collector or Source.
<p>Use the boolean field <code>_siemForward</code> to enable forwarding to SIEM.</p>
   </td>
   <td>modifiable
   </td>
  </tr>
  <tr>
   <td>base_url
   </td>
   <td>String
   </td>
   <td>Yes
   </td>
   <td>
   </td>
   <td>Provide your 1Password customer-specific domain, such as, <code>events.1password.com</code>.
   </td>
   <td>modifiable
   </td>
  </tr>
  <tr>
   <td>api_token
   </td>
   <td>String
   </td>
   <td>Yes
   </td>
   <td>
   </td>
   <td>Provide the 1Password API token you want to use to authenticate collection requests.
   </td>
   <td>modifiable
   </td>
  </tr>
  <tr>
   <td>supported_apis
   </td>
   <td>Array of strings
   </td>
   <td>Yes
   </td>
   <td>
   </td>
   <td>Define one or more of the available APIs to collect: <code>itemUsage</code>, and <code>sign-in</code>.
<p>For example, for both you'd use:</p>
<p><code>["itemUsage","sign-in"]</code></p>
   </td>
   <td>modifiable
   </td>
  </tr>
</table>

1Password Source JSON example:

```json
{
  "api.version":"v1",
  "source":{
    "schemaRef":{
      "type":"1Password"
    },
    "config":{
      "name": "1Pass",
      "base_url": "events.1password.com",
      "supported_apis": ["itemUsage","sign-in"],
      "api_token": "********",
      "fields": {
           "_siemForward": true
      }
    },
    "sourceType":"Universal"
  }
}
