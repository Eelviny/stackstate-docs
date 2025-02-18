# Requirements

## Kubernetes

StackState can be installed on a Kubernetes cluster using the Helm charts provided by StackState. These charts have been tested and are compatible with Kubernetes 1.17.x, 1.18.x and 1.19.x \(tested on Amazon EKS and Azure AKS\), or the equivalent OpenShift release \(version 4.4, 4.5 or 4.6\) and Helm 3.

For a list of all docker images used see the [image overview](installation/kubernetes_install/image_configuration.md).

### Node sizing

For a standard deployment, the StackState Helm chart will deploy backend services in a redundant setup with 3 instances of each service. The nodes required for different environments:

{% tabs %}
{% tab title="Recommended high availability setup" %}
* **Virtual machines:** 6 nodes with `32GB memory`, `8 vCPUs`
* **Amazon EKS:** 6 instances of type `m5.2xlarge` or `m4.2xlarge`
* **Azure AKS:** 6 instances of type `D8s v3` or `D8as V4` \(Intel or AMD CPUs\)
{% endtab %}

{% tab title="Minimal high availability setup" %}
* **Virtual machines:** 5 nodes with `32GB memory`, `8 vCPUs`
* **Amazon EKS:** 5 instances of type `m5.2xlarge` or `m4.2xlarge`
* **Azure AKS:** 5 instances of type `D8s v3` or `D8as V4` \(Intel or AMD CPUs\)
{% endtab %}

{% tab title="Non-high availability setup" %}
Optionally, a [non-high availability setup](installation/kubernetes_install/non_high_availability_setup.md) can be configured which has the following requirements:

* **Virtual machines:** 3 nodes with `32GB memory`, `8 vCPUs`
* **Amazon EKS:** 3 instances of type `m5.2xlarge` or `m4.2xlarge`
* **Azure AKS:** 3 instances of type `D8s v3` or `D8as V4` \(Intel or AMD CPUs\)
{% endtab %}
{% endtabs %}

### Storage

StackState uses persistent volume claims for the services that need to store data. The default storage class for the cluster will be used for all services unless this is overridden by values specified on the command line or in a `values.yaml` file. All services come with a pre-configured volume size that should be good to get you started, but can be customized later using variables as required.

For more details on the defaults used, see the page [Configure storage](installation/kubernetes_install/storage.md).

### Ingress

By default, the StackState Helm chart will deploy a router pod and service. This service's port `8080` is the only entry point that needs to be exposed via Ingress. You can access StackState without configuring Ingress by forwarding this port:

```text
kubectl port-forward service/<helm-release-name>-distributed-router 8080:8080
```

When configuring Ingress, make sure to allow for large request body sizes \(50MB\) that may be sent occasionally by data sources like the StackState Agent or the AWS integration.

For more details on configuring Ingress, have a look at the page [Configure Ingress docs](installation/kubernetes_install/ingress.md).

### Namespace resource limits

It is not recommended to set a ResourceQuota as this can interfere with resource requests. The resources required by StackState will vary according to the features used, configured resource limits and dynamic usage patterns, such as Deployment or DaemonSet scaling.

If it is necessary to set a ResourceQuota for your implementation, the namespace resource limit should be set to match the node sizing requirements. For example, using the recommended node sizing for virtual machines \(5 nodes with `32GB memory`, `8 vCPUs`\), the namespace resource limit should be `5*32GB = 160GB` and `5*8 vCPUs = 40 vCPUs`.

## Linux

### Server requirements

#### Operating system

One of the following operating systems running Java. Check also the specific requirements for the [StackState Agent V2 StackPack](../stackpacks/integrations/agent.md):

| OS | Release |
| :--- | :--- |
| Ubuntu | Bionic |
| Ubuntu | Xenial |
| Ubuntu | Trusty |
| Fedora | 28 |
| CentOS | 7 |
| Debian | Stretch |
| Red Hat | 7.5 |
| Amazon Linux | 2 |

#### Java

OpenJDK 8 **patch level 121** or later.

{% hint style="info" %}
StackState **does not work** with JDK versions 9 or higher at this time.
{% endhint %}

### Size requirements

* [Production setup](requirements.md#production-setup)
* [POC setup](requirements.md#poc-setup)
* [Development setup](requirements.md#development-setup)

#### Production setup

The StackState production setup runs on two machines and requires:

**StackState node**:

* 32GB of RAM
* 500GB disk space
* 8 cores CPU

**StackGraph node**:

* 24GB of RAM
* 500GB disk space
* 8 cores CPU

#### POC setup

The POC setup runs on a single node and requires:

* 32GB of RAM
* 500GB disk space
* 8 cores CPU

#### Development setup

The development setup runs on a single node and requires:

* 16GB of RAM
* 500GB disk space
* 4 cores CPU

### AWS requirements

To meet StackState minimal requirements, the AWS instance type needs to have at least:

* 4 CPU cores
* 16GB of memory, e.g., m5.xlarge.

The AWS CLI has to be installed on the EC2 instance that is running StackState.

## Networking

Listed ports are TCP ports.

### Production deployment

A production deployment separates StackState and StackState's database processes; StackGraph.

StackState has to be reachable on port 7070 by any supported browser. StackState port 7077 must be reachable from any system that is pushing data to StackState

StackGraph should be reachable by StackState on ports 2181, 8020, 15165, 16000, 16020, 50010.

The following ports can be opened for monitoring, but are also useful when troubleshooting:

* **StackState:** 9010, 9011, 9020, 9021, 9022, 9023, 9024, 9025, 9026
* **StackGraph:** 9001, 9002, 9003, 9004, 9005, 9006, 16010, 16030, 50070, 50075

### Development/POC deployment

StackState has to be reachable on port 7070 by any supported browser. StackState port 7077 must be reachable from any system that is pushing data to StackState

The following ports can be opened for monitoring, but are also useful when troubleshooting: 9001, 9002, 9003, 9004, 9005, 9006, 9010, 9011, 9020, 9021, 9022, 9023, 9024, 9025, 9026, 16010, 16030, 50070, 50075

### Port list per process

Detailed information about ports per process.

<table>
  <thead>
    <tr>
      <th style="text-align:left">PROCESS</th>
      <th style="text-align:left">PORT LIST</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b>Elasticsearch</b>
      </td>
      <td style="text-align:left">
        <p>9200: HTTP api</p>
        <p>9300: Native api</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>HBase Master</b>
      </td>
      <td style="text-align:left">
        <p>16000: Master client API (needs to be open for clients)</p>
        <p>16010: Master Web UI (optional)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>HBase Region Server</b>
      </td>
      <td style="text-align:left">
        <p>16020: Region client API (needs to be open for clients)</p>
        <p>16030: Region Web UI (optional)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>HDFS DataNode</b>
      </td>
      <td style="text-align:left">
        <p>50010: Datanode API (needs to be open for clients)</p>
        <p>50020: IPC api (communication within HDFS cluster)</p>
        <p>50075: HTTP api (optional)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>HDFS NameNode</b>
      </td>
      <td style="text-align:left">
        <p>8020: File system (needs to be open for clients)</p>
        <p>50070: Web UI (optional)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Kafka</b>
      </td>
      <td style="text-align:left">9092: Client port</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Receiver</b>
      </td>
      <td style="text-align:left">7077: HTTP agent API (aka receiver API). When using an agent, data is
        sent to this endpoint.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>StackGraph ProcessManager</b>
      </td>
      <td style="text-align:left">5152: StackGraph ProcessManager, at the moment only from localhost</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>StackState</b>
      </td>
      <td style="text-align:left">
        <p>7070: HTTP api &amp; user interface</p>
        <p>7071: Admin API for health checks and admin operations. Typically you
          want to use this only from `localhost`</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>StackState ProcessManager</b>
      </td>
      <td style="text-align:left">5154: StackState ProcessManager, at the moment only from localhost</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Tephra Transaction service</b>
      </td>
      <td style="text-align:left">15165: Client API</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Zookeeper</b>
      </td>
      <td style="text-align:left">
        <p>2181: Client API</p>
        <p>2888: Zookeeper peers (general communication), only when running a cluster</p>
        <p>3888: Zookeeper peers (leader election), only when running a cluster</p>
      </td>
    </tr>
  </tbody>
</table>

## Client \(browser\)

To use the StackState GUI, you must use one of the following web browsers:

* Chrome
* Firefox

