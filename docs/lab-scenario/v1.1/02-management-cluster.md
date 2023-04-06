## Create a Kubernetes cluster for Rancher

Rancher can run on any Kubernetes cluster and distribution that is certified to be standard compliant by the Cloud Native Computing Foundation (CNCF).

We recommend using a [K3s](https://k3s.io/) Kubernetes cluster. K3s is a lightweight Kubernetes distribution, which is easy and fast to install and upgrade and has a low resource consumption. You can run it in your datacenter, in the cloud as well as on edge devices. It works great on a single-node as well in large, highly available setups.

In this Rodeo we want to create a single node Kubernetes cluster on the `Rancher01` VM in order to install Rancher into it. This can be accomplished with the default K3s installation script:

```ctr:Rancher01
curl -sfL https://get.k3s.io | \
  INSTALL_K3S_CHANNEL="v1.23" K3S_KUBECONFIG_MODE="644" sh -
```

You can find more information on this in the [K3s documentation](https://rancher.com/docs/k3s/latest/en/installation/).

## Testing your cluster

K3s now created a new Kubernetes cluster and installed the kubectl CLI that you can directly use to interact with Kubernetes API.

In order to test that we can properly interact with our cluster, we can execute two commands.

To list all the nodes in the cluster and check their status:

```ctr:Rancher01
kubectl get nodes
```

The cluster should have one node, and the status should be "Ready".

To list all the Pods in all Namespaces of the cluster:

```ctr:Rancher01
kubectl get pods --all-namespaces
```

All Pods shoud have the status "Running", or "Completed".
