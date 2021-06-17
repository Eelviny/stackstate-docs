---
description: StackState core integration
---

# Splunk health Agent check

## Overview

The StackState Splunk health integration collects health from Splunk by executing Splunk saved searches that have been specified in the StackState Agent V2 Splunk health check configuration. In order to receive Splunk health data in StackState, you will therefore need to add configuration to both Splunk and the StackState Agent V2.

* [In Splunk](#splunk-saved-search), there should be at least one saved search that generates the health data you want to retrieve.
* [In StackState Agent V2](#agent-check), a Splunk health check should be configured to connect to your Splunk instance and execute the relevant Splunk saved searches.

The Splunk health check on StackState Agent V2 will execute all configured Splunk saved searches periodically to retrieve a snapshot of the health at the current time.

## Splunk saved search

### Fields used

StackState Agent V2 executes the Splunk saved searches configured in the [Splunk health Agent check configuration file](#agent-check) and pushes retrieved data to StackState. The fields from the results of a saved search that are sent to StackState for as health information in the table below.

| Field | Type | Description |
| :--- | :--- | :--- | :--- | :--- |
| **check_state_id** | string | Required, The unique identifier for the check state.  |
| **name** | string | Required. Display name for the check state.  |
| **health** | string | Required. The health value of the check state. Can be clear, deviating or critical. |
| **topology_element_identifier** | string | Required. The identifier of the component/relation this check state belongs to. |
| **message** | string | Optional. Extended message associated with the check state, supports markdown. |

### Example query

{% tabs %}
{% tab title="Splunk query for components" %}
```text
| loadjob savedsearch=:disks
| search OrganizationPart="*" OrgGrp="*" company="*"
| table host disk available_pct
| eval check_state_id = strcat host "_" disk
| eval name = disk
| eval health = case(available_pct == 0, "critical", true, "clear") 
| eval topology_element_identifier = host
| table check_state_id name health topology_element_identifier
```
{% endtab %}
{% endtabs %}

## Agent check

### Configure the Splunk health check

To enable the Splunk health integration and begin collecting health data from your Splunk instance, the Splunk health check must be configured on StackState Agent V2. The check configuration provides all details required for the Agent to connect to your Splunk instance and execute a Splunk saved search.

{% hint style="info" %}
Example Splunk health Agent check configuration file:<br />[splunk_health/conf.yaml.example \(github.com\)](https://github.com/StackVista/stackstate-agent-integrations/blob/master/splunk_health/conf.yaml.example)
{% endhint %}

To configure the Splunk health Agent check:

1. Edit the StackState Agent V2 configuration file `/etc/stackstate-agent/conf.d/splunk_health.d/conf.yaml`.
2. Under **instances**, add details of your Splunk instance:
   * **url** - The URL of your Splunk instance.
   * **authentication** - How the Agent should authenticate with your Splunk instance. Choose either token-based (recommended) or basic authentication. For details, see [authentication configuration details](/stackpacks/integrations/splunk/splunk_stackpack.md#authentication).
   * **ignore_saved_search_errors** - Set to `false` to return an error if one of the configured saved searches does not exist. Default `true`.
3. Under **saved_searches**, add details of each Splunk saved search that the check should execute to retrieve health information: 
     * **name** - The name of the [Splunk saved search](#splunk-saved-search) to execute.
       * **match** - Regex used for selecting Splunk saved search queries. Default `"health.*"`.
       * **app** - The Splunk app in which the saved searches are located. Default `"search"`.
       * **request_timeout_seconds** - Default `10`.
       * **search_max_retry_count** - Default `5`.
       * **search_seconds_between_retries** - Default `1`.
       * **batch_size** - Default `1000`.
       * **parameters** - Used in the Splunk API request. The default parameters provided make sure the Splunk saved search query refreshes. Default `force_dispatch: true` and `dispatch.now: true`.
4. More advanced options can be found in the [example configuration \(github.com\)](https://github.com/StackVista/stackstate-agent-integrations/blob/master/splunk_health/conf.yaml.example). 
6. Save the configuration file.
7. Restart StackState Agent V2 to apply the configuration changes.
8. Observe the data in the stackstate UI as check states on components and relations
9. To more closely inspect what the synchronization is doing, [use the cli](/configure/health/debug-health-sync.md)

### Disable the Agent check

To disable the Splunk health Agent check:

1. Remove or rename the Agent integration configuration file, for example:

   ```text
    mv /etc/stackstate-agent/conf.d/splunk_health.d/conf.yaml /etc/stackstate-agent/conf.d/splunk_health.d/conf.yaml.bak
   ```

2. Restart StackState Agent V2 to apply the configuration changes.

## See also

* [StackState Agent V2](/stackpacks/integrations/agent.md)
* [StackState Splunk integration details](/stackpacks/integrations/splunk/splunk_stackpack.md)
* [Health synchronization](/configure/health/health-synchronization.md)
* [Debug health synchronization](/configure/health/debug-health-sync.md)
* [Example Splunk health V2 configuration file - splunk_health.yaml \(github.com\)](https://github.com/StackVista/stackstate-agent-integrations/blob/master/splunk_health/conf.yaml.example)
* [Splunk default fields \(docs.splunk.com\)](https://docs.splunk.com/Documentation/Splunk/6.5.2/Data/Aboutdefaultfields)