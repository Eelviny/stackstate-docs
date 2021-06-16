---
description: StackState core integration
---

# Splunk topology V2 Agent check

{% hint style=“info” %} 

This page explains how to set up the Splunk topology integration together with [StackState Agent V2](/stackpacks/integrations/agent.md). 

If you are running the StackState API-Integration Agent, see the instructions on how to:
* [Configure the Splunk topology V1 check on the StackState API-Integration Agent](/stackpacks/integrations/splunk/splunk_topology.md)  
* [Upgrade to the Splunk topology V2 integration](/stackpacks/integrations/splunk/splunk_topology_upgrade_v1_to_v2.md)
{% endhint %}

## Overview

The StackState Splunk integration collects topology from Splunk by executing Splunk saved searches that have been specified in the StackState Agent V2 Splunk topology check configuration. In order to receive Splunk topology data in StackState, you will therefore need to add configuration to both Splunk and the StackState Agent V2.

* [In Splunk](#splunk-saved-search), there should be at least one saved search that generates the topology data you want to retrieve.
* [In StackState Agent V2](#agent-check), a Splunk topology check should be configured to connect to your Splunk instance and execute the relevant Splunk saved searches.

The Splunk topology check on StackState Agent V2 will execute all configured Splunk saved searches periodically to retrieve a snapshot of the topology at the current time.

## Splunk saved search

### Fields used

StackState Agent V2 executes the Splunk saved searches configured in the [Splunk topology Agent check configuration file](#agent-check) and pushes retrieved data to StackState components and relations. The fields from the results of a saved search that are sent to StackState for topology components and relations are listed in the table below.

| Field | Components | Relations | Type | Description |
| :--- | :--- | :--- | :--- | :--- |
| **type** | ✅ | ✅ | string | Required, The type of component or relation.  |
| **id** | ✅ | - | string | Required. The unique identifier for the component.  |
| **identifier.&lt;identifier\_name&gt;**  | ✅ | - | string | Optional. The value will be included as identifier of the component. |
| **label.&lt;label\_name&gt;** | ✅ | - | string | Optional. The value will be added as a label on the component in the format `label_name:value` |
| **name** | ✅ | - | string | Required. The value will be used as the component name. |
| All other fields | ✅ | - | - | [Splunk default fields \(docs.splunk.com\)](https://docs.splunk.com/Documentation/Splunk/6.5.2/Data/Aboutdefaultfields) other than `_time` will be filtered out of the result.<br />Any other fields present in the result will be available in StackState in the `data` field of the component properties `source` tab. |
| **sourceId** | - | ✅ | string | Required. The ID of the component that is the source of the relation. |
| **targetId** | - | ✅ | string | Required. The ID of the component that is the target of the relation.  |

### Example queries

{% tabs %}
{% tab title="Splunk query for components" %}
```text
| loadjob savedsearch=:servers
| search OrganizationPart="*" OrgGrp="*" company="*"
| table name | dedup name
| eval name = upper(name)
| eval id = 'name', type="vm"
| table id type name
```
{% endtab %}
{% endtabs %}

The example Splunk saved search above would result in the following topology component data in StackState:

| Field | Data |
| :--- | :--- |
| **type** | Splunk `type` field.  |
| **id** | Splunk `id` field. |
| **identifier.&lt;identifier\_name&gt;** | - |
| **label.&lt;label\_name&gt;** | - |
| **name** | Splunk `name` field.|

{% tabs %}
{% tab title="Splunk query for relations" %}
```text
index=cmdb_icarus source=cmdb_ci_rel earliest=-3d
| eval VMName=lower(VMName)
| rename Application as sourceId, VMName as targetId
| eval type="is-hosted-on"
| table sourceId targetId type
```
{% endtab %}
{% endtabs %}

The example Splunk saved search above would result in the following topology relation data in StackState:

| Field | Data |
| :--- | :--- |
| **type** | Splunk `type` field.  |
| **sourceId** | `<sourceId>` (renamed from `Application`) |
| **targetId** | `<targetId>` (renamed from `VMName`) |

## Agent check

### Configure the Splunk topology check

To enable the Splunk topology integration and begin collecting component and relation data from your Splunk instance, the Splunk topology check must be configured on StackState Agent V2. The check configuration provides all details required for the Agent to connect to your Splunk instance and execute a Splunk saved search.

{% hint style="info" %}
Example Splunk topology Agent check configuration file:<br />[splunk_topology/conf.yaml.example \(github.com\)](https://github.com/StackVista/stackstate-agent-integrations/blob/master/splunk_topology/conf.yaml.example)
{% endhint %}

To configure the Splunk topology Agent check:

1. Edit the StackState Agent V2 configuration file `/etc/stackstate-agent/conf.d/splunk_topology.d/conf.yaml`.
2. Under **instances**, add details of your Splunk instance:
   * **url** - The URL of your Splunk instance.
   * **authentication** - How the Agent should authenticate with your Splunk instance. Choose either token-based (recommended) or basic authentication. For details, see [authentication configuration details](/stackpacks/integrations/splunk/splunk_stackpack.md#authentication).
   * **ignore_saved_search_errors** - Set to `false` to return an error if one of the configured saved searches does not exist. Default `true`.
   * **tags** - Optional. Can be used to apply specific tags to all reported topology in StackState.
3. Under **component_saved_searches**, add details of each Splunk saved search that the check should execute to retrieve components: 
     * **name** - The name of the [Splunk saved search](#splunk-saved-search) to execute.
       * **match** - Regex used for selecting Splunk saved search queries. Default `"comp.*"` for component queries and `"relation*"` for relation queries.
       * **app** - The Splunk app in which the saved searches are located. Default `"search"`.
       * **request_timeout_seconds** - Default `10`.
       * **search_max_retry_count** - Default `5`.
       * **search_seconds_between_retries** - Default `1`.
       * **batch_size** - Default `1000`.
       * **parameters** - Used in the Splunk API request. The default parameters provided make sure the Splunk saved search query refreshes. Default `force_dispatch: true` and `dispatch.now: true`.
4. Under **relation_saved_searches**, add details of each Splunk saved search that the check should execute to retrieve relations.
5. More advanced options can be found in the [example configuration \(github.com\)](https://github.com/StackVista/stackstate-agent-integrations/blob/master/splunk_topology/conf.yaml.example). 
6. Save the configuration file.
7. Restart StackState Agent V2 to apply the configuration changes.
8. Once the Agent has restarted, wait for the Agent to collect data and send it to StackState.

### Disable the Agent check

To disable the Splunk topology Agent check:

1. Remove or rename the Agent integration configuration file, for example:

   ```text
    mv /etc/stackstate-agent/conf.d/splunk_topology.d/conf.yaml /etc/stackstate-agent/conf.d/splunk_topology.d/conf.yaml.bak
   ```

2. Restart StackState Agent V2 to apply the configuration changes.

## See also

* [StackState Agent V2](/stackpacks/integrations/agent.md)
* [StackState Splunk integration details](/stackpacks/integrations/splunk/splunk_stackpack.md)
* [Splunk Topology V1 - API-Integration Agent](/stackpacks/integrations/splunk/splunk_topology.md)
* [Example Splunk Topology V2 configuration file - splunk_topology.yaml \(github.com\)](https://github.com/StackVista/stackstate-agent-integrations/blob/master/splunk_topology/conf.yaml.example)
* [Splunk default fields \(docs.splunk.com\)](https://docs.splunk.com/Documentation/Splunk/6.5.2/Data/Aboutdefaultfields)