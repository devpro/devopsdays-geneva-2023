## Creating a Kubernetes cluster for management tools

We can run any distribution that is certified to be standard compliant by the Cloud Native Computing Foundation (CNCF).

We'll be [K3s](https://k3s.io/) as Kubernetes distribution. K3s is a lightweight Kubernetes distribution, which is easy and fast to install and upgrade and has a low resource consumption. You can run it in your datacenter, in the cloud as well as on edge devices. It works great on a single-node as well in large, highly available setups.

```ctr:Management01
curl -sfL https://get.k3s.io | \
  INSTALL_K3S_CHANNEL="v1.23" K3S_KUBECONFIG_MODE="644" sh -
```

## Testing your cluster

K3s now created a new Kubernetes cluster and installed the kubectl CLI that you can directly use to interact with Kubernetes API.

In order to test that we can properly interact with our cluster, we can execute two commands.

To list all the nodes in the cluster and check their status:

```ctr:Management01
kubectl get nodes
```

The cluster should have one node, and the status should be "Ready".

To list all the Pods in all Namespaces of the cluster:

```ctr:Management01
kubectl get pods --all-namespaces
```

All Pods shoud have the status "Running", or "Completed".
