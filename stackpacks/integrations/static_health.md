---
description: StackState curated integration
---

# Static Health

## Overview

The Static Health integration is used to visualize static health information in StackState by reading from CSV files. The health consists of check states.


## Setup

### Prerequisites

To set up the Static Health integration you will need to have:
 * StackState [Agent V2](/setup/agent/about-stackstate-agent.md) installed on a machine that can connect to StackState.

### Configure

To configure the StackState Agent to read CSV health files: 

1. Edit the Agent integration configuration file `/etc/stackstate-agent/conf.d/static_health.d/conf.yaml` to include the following details:
    * **type** - set to `csv` for parsing CSV typed files.
    * **health_file** - the absolute path to the CSV file containing health state information.
    * **delimiter** - the delimiter used in the CSV file.

     ```text
     init_config:
    
     instances:
     - type: csv
       health_file: /path/to/health.csv
       delimiter: ';'
     ```

2. [Restart the StackState Agent\(s\)](/setup/agent/about-stackstate-agent.md#run-stackstate-agent-v2) to apply the configuration changes.
3. Once the Agent has restarted, wait for the Agent to collect data from the specified [health CSV file](#csv-file-format) and send it to StackState.

## CSV file format

### Fields
Static health is read from a CSV file with a header row, that specifies the fields that are included in the file. The available fields are listed in the table below.

| Field name | Mandatory | Description |
|:---|:---|:---|
| **check_state_id** | yes |  Identification for the check state within the health stream. |
| **name** | yes | The display name of the check state. |
| **health** | yes | The health state of the check state. Can be clear, deviating or critical. |
| **topology_element_identifier** | yes | Identifier of the component or relation the check state will be attached to.  |
| **message** | no | Additional descriptive message of the check state. |

{% tabs %}
{% tab title="Example health CSV file" %}
```
check_state_id,name,health,topology_element_identifier,message
check_1,Example check,critical,urn:component/some_component,Something went wrong
check_2,Another example check,clear,urn:component/some_component,This is going well
```
{% endtab %}
{% endtabs %}

### Delimiter

The delimiter used in the CSV file can be specified when you [configure the Static Health check](#configure) on the StackState Agent.


## See also

* [StackState Agent V2](/setup/agent/about-stackstate-agent.md)
* [Health state in StackState](/use/health-state/health-state-in-stackstate.md)
* [Health Synchronization](/configure/health/health-synchronization.md)
* [Debug Health Synchronization](/configure/health/debug-health-sync.md)
