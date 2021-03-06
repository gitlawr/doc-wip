---
title: Launching Kubernetes on Existing Custom Nodes
---

When you create a custom cluster, Rancher uses RKE (the Rancher Kubernetes Engine) to create a Kubernetes cluster in on-premise bare-metal servers, on-premise virtual machines, or in any node hosted by an infrastructure provider.

To use this option you'll need access to servers you intend to use in your Kubernetes cluster. Provision each server according to the [requirements](/docs/cluster-provisioning/node-requirements), which includes some hardware specifications and Docker. After you install Docker on each server, run the command provided in the Rancher UI to turn each server into a Kubernetes node.

This section describes how to set up a custom cluster.

## Creating a Cluster with Custom Nodes

> **Want to use Windows hosts as Kubernetes workers?**
>
> See [Configuring Custom Clusters for Windows](/docs/cluster-provisioning/rke-clusters/windows-clusters/) before you start.

<!-- TOC -->

- [1. Provision a Linux Host](#1-provision-a-linux-host)
- [2. Create the Custom Cluster](#2-create-the-custom-cluster)
- [3. Amazon Only: Tag Resources](#3-amazon-only-tag-resources)

<!-- /TOC -->

#### 1. Provision a Linux Host

Begin creation of a custom cluster by provisioning a Linux host. Your host can be:

- A cloud-host virtual machine (VM)
- An on-premise VM
- A bare-metal server

If you want to reuse a node from a previous custom cluster, [clean the node](/docs/admin-settings/removing-rancher/rancher-cluster-nodes/) before using it in a cluster again. If you reuse a node that hasn't been cleaned, cluster provisioning may fail.

Provision the host according to the [installation requirements](/docs/cluster-provisioning/node-requirements) and the [checklist for production-ready clusters.](/docs/cluster-provisioning/production)

#### 2. Create the Custom Cluster

1. From the **Clusters** page, click **Add Cluster**.

2. Choose **Custom**.

3. Enter a **Cluster Name**.

4. {{< step_create-cluster_member-roles >}}

5. {{< step_create-cluster_cluster-options >}}

   > **Using Windows nodes as Kubernetes workers?**
   >
   > - See [Enable the Windows Support Option](/docs/cluster-provisioning/rke-clusters/windows-clusters/#enable-the-windows-support-option).
   > - The only Network Provider available for clusters with Windows support is Flannel. See [Networking Option](/docs/cluster-provisioning/rke-clusters/windows-clusters/#networking-option).

6. <a id="step-6"></a>Click **Next**.

7. From **Node Role**, choose the roles that you want filled by a cluster node.

   > **Notes:**
   >
   > - Using Windows nodes as Kubernetes workers? See [Node Configuration](/docs/cluster-provisioning/rke-clusters/windows-clusters/#node-configuration).
   > - Bare-Metal Server Reminder: If you plan on dedicating bare-metal servers to each role, you must provision a bare-metal server for each role (i.e. provision multiple bare-metal servers).

8. <a id="step-8"></a>**Optional**: Click **[Show advanced options](/docs/admin-settings/agent-options/)** to specify IP address(es) to use when registering the node, override the hostname of the node, or to add [labels](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) or [taints](https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/) to the node.

9. Copy the command displayed on screen to your clipboard.

10. Log in to your Linux host using your preferred shell, such as PuTTy or a remote Terminal connection. Run the command copied to your clipboard.

    > **Note:** Repeat steps 7-10 if you want to dedicate specific hosts to specific node roles. Repeat the steps as many times as needed.

11. When you finish running the command(s) on your Linux host(s), click **Done**.

{{< result_create-cluster >}}

#### 3. Amazon Only: Tag Resources

If you have configured your cluster to use Amazon as **Cloud Provider**, tag your AWS resources with a cluster ID.

[Amazon Documentation: Tagging Your Amazon EC2 Resources](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html)

> **Note:** You can use Amazon EC2 instances without configuring a cloud provider in Kubernetes. You only have to configure the cloud provider if you want to use specific Kubernetes cloud provider functionality. For more information, see [Kubernetes Cloud Providers](https://kubernetes.io/docs/concepts/cluster-administration/cloud-providers/)

The following resources need to tagged with a `ClusterID`:

- **Nodes**: All hosts added in Rancher.
- **Subnet**: The subnet used for your cluster
- **Security Group**: The security group used for your cluster.

      	>**Note:** Do not tag multiple security groups. Tagging multiple groups generates an error when creating Elastic Load Balancer.

The tag that should be used is:

```
Key=kubernetes.io/cluster/<CLUSTERID>, Value=owned
```

`<CLUSTERID>` can be any string you choose. However, the same string must be used on every resource you tag. Setting the tag value to `owned` informs the cluster that all resources tagged with the `<CLUSTERID>` are owned and managed by this cluster.

If you share resources between clusters, you can change the tag to:

```
Key=kubernetes.io/cluster/CLUSTERID, Value=shared
```

## Optional Next Steps

After creating your cluster, you can access it through the Rancher UI. As a best practice, we recommend setting up these alternate ways of accessing your cluster:

- **Access your cluster with the kubectl CLI:** Follow [these steps](/docs/cluster-admin/cluster-access/kubectl/#accessing-clusters-with-kubectl-on-your-workstation) to access clusters with kubectl on your workstation. In this case, you will be authenticated through the Rancher server’s authentication proxy, then Rancher will connect you to the downstream cluster. This method lets you manage the cluster without the Rancher UI.
- **Access your cluster with the kubectl CLI, using the authorized cluster endpoint:** Follow [these steps](/docs/cluster-admin/cluster-access/kubectl/#authenticating-directly-with-a-downstream-cluster) to access your cluster with kubectl directly, without authenticating through Rancher. We recommend setting up this alternative method to access your cluster so that in case you can’t connect to Rancher, you can still access the cluster.
