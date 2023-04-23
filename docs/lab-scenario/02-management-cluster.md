## Creating a Kubernetes cluster for management tools

ℹ _[K3s](https://k3s.io/) is a lightweight Kubernetes distribution, which is easy and fast to install and upgrade and has a low resource consumption. It is certified to be standard compliant by the Cloud Native Computing Foundation (CNCF)_

Open the `management01` tab (on the right) and click on the command to run it in the terminal window:

```ctr:Management01
curl -sfL https://get.k3s.io | \
  INSTALL_K3S_CHANNEL="v1.23" K3S_KUBECONFIG_MODE="644" sh -
```

## Testing your cluster

ℹ _K3s created a new Kubernetes cluster and installed the kubectl CLI that you can directly use to interact with Kubernetes API_

To list all the nodes in the cluster and check their status:

```ctr:Management01
kubectl get nodes
```

> The cluster should have one node, and the status should be "Ready"

To list all the pods in all namespaces of the cluster:

```ctr:Management01
kubectl get pods --all-namespaces
```

> Wait until all pods have the status "Running", or "Completed"
