---
description: StackState core integration
stackpack-name: OpenShift
---

# OpenShift

## Overview

The OpenShift integration is used to create a near real-time synchronization of topology and associated internal services from an OpenShift cluster to StackState. This StackPack allows monitoring of the following:

* Workloads
* Nodes, pods, containers and services
* Configmaps, secrets and volumes

![Data flow](../../.gitbook/assets/stackpack-openshift.svg)

The OpenShift integration collects topology data in an OpenShift cluster as well as metrics and events.

* Data is retrieved by the deployed [StackState Agents](/setup/agent/kubernetes.md#stackstate-agents) and then pushed to StackState via the Agent StackPack and the OpenShift StackPack.
* In StackState:
  * [Topology data](openshift.md#topology) is translated into components and relations.
  * [Tags](openshift.md#tags) defined in OpenShift are added to components and relations in StackState.
  * Relevant [metrics data](openshift.md#metrics) is mapped to associated components and relations in StackState. All retrieved metrics data is stored and accessible within StackState.
  * [Events](openshift.md#events) are available in the StackState Events Perspective and listed in the details pane of the StackState UI.

## Setup

### Prerequisites

The following prerequisites are required to install the OpenShift StackPack and deploy the StackState Agent and Cluster Agent:

* An OpenShift Cluster must be up and running.
* A recent version of Helm 3.
* A user with permissions to create privileged pods, ClusterRoles, ClusterRoleBindings and SCCs:
  * ClusterRole and ClusterRoleBinding are needed to grant StackState Agents permissions to access the OpenShift API.
  * StackState Agents need to run in a privileged pod to be able to gather information on network connections and host information.

### Install

Install the OpenShift StackPack from the StackState UI **StackPacks** &gt; **Integrations** screen. You will need to provide the following parameters:

* **OpenShift Cluster Name** - A name to identify the cluster. This does not need to match the cluster name used in `kubeconfig`, however, that is usually a good candidate for a unique name.

If the Agent StackPack is not already installed, this will be automatically installed together with the OpenShift StackPack. This is required to work with the StackState Agent, which will need to be deployed on each node in the OpenShift cluster.

### Deploy the StackState Agent and Cluster Agent

For the OpenShift integration to retrieve topology, events and metrics data, you will need to have the following installed on your OpenShift cluster:

* A StackState Agent on each node in the cluster
* StackState Cluster Agent on one node
* kube-state-metrics

Follow the instructions to [deploy StackState Agent V2, the Cluster Agent and kube-state-metrics](/setup/agent/kubernetes.md).

{% hint style="info" %}
To integrate with other services, a separate instance of the [StackState Agent](/setup/agent/about-stackstate-agent.md) should be deployed on a standalone VM. It is not currently possible to configure a StackState Agent deployed on an OpenShift cluster with checks that integrate with other services.
{% endhint %}

### Status

To check the status of the OpenShift integration, check that the StackState Cluster Agent \(`cluster-agent`\) pod and all of the StackState Agent \(`cluster-agent-agent`\) pods have status ready.

```text
❯ kubectl get deployment,daemonset --namespace stackstate

NAME                                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/stackstate-cluster-agent             1/1     1            1           5h14m
NAME                                                 DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
daemonset.apps/stackstate-cluster-agent-agent        10        10        10      10           10          <none>          5h14m
```

## Checks

### Cluster checks

Optionally, the chart can be configured to start additional StackState Agent V2 pods (1 by default) as StackState ClusterCheck Agent pods that run cluster checks. Cluster checks are configured on the [StackState Cluster Agent](#stackstate-cluster-agent) are run by one of the deployed [StackState ClusterCheck Agent](#stackstate-clustercheck-agent) pods.

#### Enable cluster checks

To enable cluster checks and the cluster check Agent pods, create a `values.yaml` file to deploy the `cluster-agent` Helm chart and add the following YAML segment:

```yaml
clusterChecks:
  enabled: true
```

### Kubernetes_state check

The kubernetes_state check is responsible for gathering metrics from kube-state-metrics and sending them to StackState. It is configured on the StackState Cluster Agent and runs in the StackState Agent pod that is on the same node as the kube-state-metrics pod.

#### Run as a cluster check

In a default deployment, the pod running the StackState Cluster Agent and every deployed StackState Agent need to be able to run the check. In a large OpenShift cluster, this can consume a lot of memory as every pod must be configured with sufficient CPU and memory requests and limits. Since only one of those Agent pods will actually run the check, a lot of CPU and memory resources will be allocated, but will not be used.

To remedy that situation, the kubernetes_state check can be configured to run as a cluster check. The YAML segment below shows how to do that in the `values.yaml` file used to deploy the `cluster-agent` chart:

```yaml
clusterChecks:
# clusterChecks.enabled -- Enables the cluster checks functionality _and_ the clustercheck pods.
  enabled: true
agent:
  config:
    override:
# agent.config.override -- Disables kubernetes_state check on regular agent pods.
    - name: auto_conf.yaml
      path: /etc/stackstate-agent/conf.d/kubernetes_state.d
      data: |
clusterAgent:
  config:
    override:
# clusterAgent.config.override -- Defines kubernetes_state check for clusterchecks agents. Auto-discovery
#                                 with ad_identifiers does not work here. Use a specific URL instead.
    - name: conf.yaml
      path: /etc/stackstate-agent/conf.d/kubernetes_state.d
      data: |
        cluster_check: true

        init_config:

        instances:
          - kube_state_url: http://YOUR_KUBE_STATE_METRICS_SERVICE_NAME:8080/metrics
```


## Integration details

### Data retrieved

The OpenShift integration retrieves the following data:

* [Events](openshift.md#events)
* [Metrics](openshift.md#metrics)
* [Tags](openshift.md#tags)
* [Topology](openshift.md#topology)

#### Events

The OpenShift integration retrieves all events from the OpenShift cluster. The table below shows which event category will be assigned to each event type in StackState:

| StackState event category | OpenShift events |
| :--- | :--- |
| **Activities** | `BackOff`<br />`ContainerGCFailed`<br />`ExceededGracePeriod`<br />`FileSystemResizeSuccessful`<br />`ImageGCFailed`<br />`Killing`<br />`NodeAllocatableEnforced`<br />`NodeNotReady`<br />`NodeSchedulable`<br />`Preempting`<br />`Pulling`<br />`Pulled`<br />`Rebooted`<br />`Scheduled`<br />`Starting`<br />`Started`<br />`SuccessfulAttachVolume`<br />`SuccessfulDetachVolume`<br />`SuccessfulMountVolume`<br />`SuccessfulUnMountVolume`<br />`VolumeResizeSuccessful` |
| **Alerts** | `NotTriggerScaleUp` |
| **Changes** | `Created` \(created container\)<br />`NodeReady`<br />`SandboxChanged`<br />`SuccesfulCreate` ||
| **Others** | All other events |

#### Metrics

The OpenShift integration makes all metrics from the OpenShift cluster available in StackState. Relevant metrics are automatically mapped to the associated components.

All retrieved metrics can be browsed or added to a component as a telemetry stream. Select the data source **StackState Metrics** and type `openshift` in the **Select** box to get a full list of all available metrics.

#### Topology

The OpenShift integration retrieves components and relations for the OpenShift cluster.

**Components**

The following OpenShift topology data is available in StackState as components:

|  |  |
| :--- | :--- |
| Cluster | Ingress |
| Namespace | Job |
| Node | Persistent Volume |
| Pod | ReplicaSet |
| Container | Secret |
| ConfigMap | Service |
| CronJob | StatefulSet |
| DaemonSet | Volume |
| Deployment |  |

**Relations**

The following relations between components are retrieved:

* Container → PersistentVolume, Volume
* CronJob → Job
* DaemonSet → Pod
* Deployment → ReplicaSet
* Job → Pod
* Ingress → Service
* Namespace → CronJob, DaemonSet, Deployment, Job, ReplicaSet, Service, StatefulSet
* Node → Cluster relation
* Pod → ConfigMap, Container, Deployment, Node, PersistentVolume, Secret, Volume
* ReplicaSet →  Pod
* Service → ExternalService, Pod
* StatefulSet → Pod
* Direct communication between processes
* Process → Process communication via OpenShift service
* Process → Process communication via headless OpenShift service

#### Traces

The OpenShift integration does not retrieve any traces data.

#### Tags

All tags defined in OpenShift will be retrieved and added to the associated components and relations in StackState.

### REST API endpoints

The StackState Agent talks to the kubelet and kube-state-metrics API.

The StackState Agent and Cluster Agent connect to the OpenShift API to retrieve cluster wide information and OpenShift events. The following API endpoints used:

| Resource type | REST API endpoint |
| :--- | :--- |
| Metadata &gt; ComponentStatus | `GET /api/v1/componentstatuses` |
| Metadata &gt; ConfigMap | `GET /api/v1/namespaces/{namespace}/configmaps` |
| Metadata &gt; Event | `GET /apis/events.k8s.io/v1/events` |
| Metadata &gt; Namespace | `GET /api/v1/namespaces` |
| Network &gt; Endpoints | `GET /api/v1/namespaces/{namespace}/endpoints` |
| Network &gt; Ingress | `GET /apis/networking.k8s.io/v1/namespaces/{namespace}/ingresses` |
| Network &gt; Service | `GET /api/v1/namespaces/{namespace}/services` |
| Node &gt; Node | `GET /api/v1/nodes` |
| Security &gt; Secret | `GET /api/v1/secrets` |
| Storage &gt; PersistentVolumeClaimSpec | `GET /api/v1/namespaces/{namespace}/persistentvolumeclaims` |
| Storage &gt; VolumeAttachment | `GET /apis/storage.k8s.io/v1/volumeattachments` |
| Workloads &gt; CronJob | `GET /apis/batch/v1beta1/namespaces/{namespace}/cronjobs` |
| Workloads &gt; DaemonSet | `GET /apis/apps/v1/namespaces/{namespace}/daemonsets` |
| Workloads &gt; Deployment | `GET /apis/apps/v1/namespaces/{namespace}/deployments` |
| Workloads &gt; Job | `GET /apis/batch/v1/namespaces/{namespace}/jobs` |
| Workloads &gt; PersistentVolume | `GET /api/v1/persistentvolumes` |
| Workloads &gt; Pod | `GET /api/v1/namespaces/{namespace}/pods` |
| Workloads &gt; ReplicaSet | `GET /apis/apps/v1/namespaces/{namespace}/replicasets` |
| Workloads &gt; StatefulSet | `GET /apis/apps/v1/namespaces/{namespace}/statefulsets` |
|  | `/version` |
|  | `/healthz` |

For further details, refer to the [OpenShift API documentation \(openshift.com\)](https://docs.openshift.com/container-platform/4.4/rest_api/storage_apis/volumeattachment-storage-k8s-io-v1.html).

### Component actions

A number of [actions](../../use/stackstate-ui/perspectives/topology-perspective.md#actions) are added to StackState when the OpenShift StackPack is installed. They are available from the **Actions** section on the right of the screen when an OpenShift component is selected or from the component context menu, displayed when you hover over an OpenShift component in the Topology Perspective

| Action | Available for component types | Description |
| :--- | :--- | :--- |
| **Show configuration and storage** | pods<br />containers | Display the selected pod or container with its configmaps, secrets and volumes |
| **Show dependencies \(deep\)** | deployment<br />replicaset<br />replicationcontroller<br />statefulset<br />daemonset<br />job<br />cronjob<br />pod | Displays all dependencies \(up to 6 levels deep\) of the selected pod or workload. |
| **Show pods** | deployment<br />replicaset<br />replicationcontroller<br />statefulset<br />daemonset<br />job<br />cronjob | Displays the pods for the selected workload. |
| **Show pods & services** | namespace | Opens a view for the pods/services in the selected namespace |
| **Show services** | namespace | Open a view for the service and ingress components in the selected namespace |
| **Show workloads** | namespace | Show workloads in the selected namespace |

Details of installed actions can be found in the StackState UI **Settings** &gt; **Actions** &gt; **Component Actions** screen.

### OpenShift views in StackState

When the OpenShift integration is enabled, the following OpenShift views are available in StackState for each cluster:

* OpenShift - Applications - 
* OpenShift - Infrastructure - 
* OpenShift - Namespaces - 
* OpenShift - Workload Controllers - 

### Open source

The code for the StackState Agent OpenShift check is open source and available on GitHub at:

* [https://github.com/StackVista/stackstate-agent/tree/master/pkg/collector/corechecks/cluster](https://github.com/StackVista/stackstate-agent/tree/master/pkg/collector/corechecks/cluster)
* [https://github.com/stackvista/stackstate-agent](https://github.com/stackvista/stackstate-agent)
* [https://github.com/StackVista/stackstate-agent-integrations/tree/master/kubernetes](https://github.com/StackVista/stackstate-agent-integrations/tree/master/kubernetes)

## Troubleshooting

Troubleshooting steps for any known issues can be found in the [StackState support Knowledge base](https://support.stackstate.com/hc/en-us/search?utf8=%E2%9C%93&category=360002777619&query=OpenShift).

## Uninstall

To uninstall the OpenShift StackPack, go to the StackState UI **StackPacks** &gt; **Integrations** &gt; **OpenShift** screen and click **UNINSTALL**. All OpenShift StackPack specific configuration will be removed from StackState.

To uninstall the StackState Cluster Agent and the StackState Agent from your OpenShift cluster, run a Helm uninstall:

```text
helm uninstall <release_name> --namespace <namespace>

# If you used the standard install command provided when you installed the StackPack
helm uninstall stackstate-cluster-agent --namespace stackstate
```

## Release notes

**OpenShift StackPack v3.7.1 \(2021-04-02\)**

* Improvement: Enable auto grouping on generated views.
* Improvement: Update documentation.
* Improvement: Common bumped from 2.4.3 to 2.5.0
* Improvement: StackState min version bumped to 4.3.0

**OpenShift StackPack v3.6.0 \(2021-03-08\)**

* Feature: Namespaces are now a component in StackState with a namespaces view for each cluster
* Feature: New component actions for quick navigation on workloads, pods and namespaces
* Feature: Added a "Pod Scheduled" metric stream to pods
* Feature: Secrets are now a component in StackState
* Improvement: The "Desired vs Ready" checks on workloads now use the "Ready replicas" stream instead of the replicas stream.
* Improvement: Use standard \(blue\) Kubernetes icons
* Bug Fix: Fixed Kubernetes synchronization when a component had no labels but only tags

**OpenShift StackPack v3.5.2 \(2020-08-18\)**

* Feature: Introduced the Release notes pop up for customer

**OpenShift StackPack v3.5.1 \(2020-08-10\)**

* Feature: Introduced Kubernetes specific component identifiers

**OpenShift StackPack v3.5.0 \(2020-08-04\)**

* Improvement: Deprecated stackpack specific layers and introduced a new common layer structure.

**OpenShift StackPack v3.4.0 \(2020-06-19\)**

* Improvement: Set the stream priorities on all streams.

## See also

* [Agent StackPack](agent.md)
* [StackState Agent Kubernetes check \(github.com\)](https://github.com/StackVista/stackstate-agent-integrations/tree/master/kubernetes)
* [StackState Cluster Agent Helm Chart \(github.com\)](https://github.com/StackVista/helm-charts/tree/master/stable/cluster-agent)
* [Openshift API documentation \(openshift.com\)](https://docs.openshift.com/container-platform/4.4/rest_api/storage_apis/volumeattachment-storage-k8s-io-v1.html)
