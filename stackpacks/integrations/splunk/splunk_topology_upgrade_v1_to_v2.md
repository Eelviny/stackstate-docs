---
description: StackState core integration
---

# Splunk topology upgrade V1 to V2

## Overview

In previous releases of StackState, it was only possible to run the Splunk topology check using the StackState API-integration Agent. It is now also possible to run the Splunk topology integration through [StackState Agent V2](/stackpacks/integrations/agent.md). 

If you are currently running the Splunk topology V1 check on the [StackState API-Integrations Agent](/stackpacks/integrations/api-integration.md), this guide will help you migrate to the new StackState Agent V2 check.

If you are starting from scratch, you can directly configure the [Splunk topology V2 check](/stackpacks/integrations/splunk/splunk_topology_v2.md) on StackState Agent V2.

## Upgrade steps

1. Install [StackState Agent V2](/stackpacks/integrations/agent.md) in a location that can connect to both StackState and Splunk.
2. Stop the API-Integration Agent.
3. To disable the Splunk topology V1 check on the StackState API-Integration Agent, move the Splunk topology API-Integration configuration file to the newly installed StackState Agent V2.
   ```
   mv /etc/sts-agent/conf.d/splunk_topology.yaml /etc/stackstate-agent/conf.d/splunk_topology.d/conf.yaml
   ```
4. Edit the StackState Agent V2 configuration file `/etc/stackstate-agent/conf.d/splunk_topology.d/conf.yaml` and replace all occurrences of the following:
   - `default_polling_interval_seconds` replace with `min_collection_interval`.
   - `polling_interval_seconds` replace with `min_collection_interval`.
6. Restart StackState Agent V2 to apply the configuration changes.
   - The Splunk topology V2 check is enabled on StackState Agent V2.
   - Wait for the Agent to collect data and send it to StackState.
7. If you have other integrations configured on the StackState API-Integration Agent, start it again.

## See also

* [StackState Agent V2](/stackpacks/integrations/agent.md)
* [StackState Splunk integration details](/stackpacks/integrations/splunk/splunk_stackpack.md)
* [Splunk topology V2 integration](/stackpacks/integrations/splunk/splunk_topology_v2.md)