---
description: StackState curated integration
---

# CentOS

## Overview

StackState Agent V2 will retrieve data from the CentOS host it is running on and push this to StackState.

## Setup

### Prerequisites
 
* [StackState Agent V2](/setup/agent/linux.md) installed on a CentOS host that is able to connect to StackState.
* The [StackState Agent V2 StackPack](/stackpacks/integrations/agent.md) installed in StackState.

### Install

The CentOS integration is part of the [StackState Agent V2 StackPack](/stackpacks/integrations/agent.md).

## Data retrieved

StackState Agent V2 will synchronize the following data from the host it is running on with StackState:

- Hosts, processes and containers.
- Telemetry for hosts, processes and containers.
- For OS versions with a Network Tracer: 
    * Network connections between processes and containers.
    * Network traffic telemetry. 
    * [Golden signals](/use/metrics-events/golden_signals.md), such as HTTP server latencies, errors and request counts.

See the [supported Linux versions](/setup/agent/linux.md#supported-linux-versions) for details.

## See also

* [About the StackState Agent](/setup/agent/about-stackstate-agent.md)
* [Deploy StackState Agent V2 on Linux](/setup/agent/linux.md)