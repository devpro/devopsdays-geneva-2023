## Option 1

### Creating a Kubernetes Lab Cluster within Rancher

In this step, we will be creating a Kubernetes Lab environment from Rancher.

1. In the left menu, under **GLOBAL APPS**, click on **Cluster Management**
2. Click on **Create**
3. Under **Use existing nodes and create a cluster using RKE**, Click on **Custom**
4. Enter a name in the **Cluster Name** box
5. Click on **Next** at the bottom
6. Make sure **etcd**, **Control Plane**, and **Worker** are all ticked
7. Click on **Show advanced options** and set **Public Address** (`${vminfo:Workload01:public_ip}`) and **Internal Address** (`${vminfo:Workload01:private_ip}`)
8. Click the clipboard to **Copy to Clipboard** the `docker run` command
9. Proceed to the next step of this scenario

### Start the Rancher Kubernetes Cluster Bootstrapping Process

**IMPORTANT NOTE:** Make sure you have selected the `Workload01` tab in HobbyFarm in the window to the right. If you run this command on `Rancher01` you will cause problems for your scenario session.

1. Take the copied docker command and run it on `Workload01`
2. Once the `docker run` command is complete, you should see a message similiar to `1 node has registered`
3. Within the Rancher UI click on `<YOUR_CLUSTER_NAME>` which is the name you entered during cluster creation.
4. You can watch the state of the cluster as your Kubernetes node `Workload01` registers with Rancher here as well as the **Nodes** tab
5. Your cluster state on the Global page will change to **Active**
6. Once your cluster has gone to **Active** you can click on it and start exploring.

## Option 2

## Generate an SSH Keypair

The following command will generate the SSH keypair and copy it into the authorized_keys file, so that we can do SSH tunneling later on.

```ctr:Management01
ssh-keygen -b 2048 -t rsa -f /home/ubuntu/.ssh/id_rsa -N ""
cat /home/ubuntu/.ssh/id_rsa.pub >> /home/ubuntu/.ssh/authorized_keys
```

### Install kubectl

Kubectl is needed to interact with Kubernetes.

```ctr:Workload01
sudo curl -L https://dl.k8s.io/release/v1.26.3/bin/linux/amd64/kubectl -o /usr/local/bin/kubectl
sudo chmod +x /usr/local/bin/kubectl
```

We can test `kubectl` and make sure it is properly installed.

```ctr:Workload01
kubectl version --client --short
```

## Install Helm

Helm is a very popular package manager for Kubernetes and will be used to install components.

```ctr:Management01
sudo curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
sudo chmod 700 get_helm.sh
sudo ./get_helm.sh
sudo rm -f ./get_helm.sh
```

We can check the installation.

```ctr:Management01
helm version --client
```

## Download a Kubernetes distribution

We will use [RKE](https://github.com/rancher/rke) (Rancher Kubernetes Engine).

```ctr:Workload01
sudo wget -O /usr/local/bin/rke https://github.com/rancher/rke/releases/download/v1.4.3/rke_linux-amd64
sudo chmod +x /usr/local/bin/rke
```

Validate that RKE CLI is installed properly.

```ctr:Workload01
rke -v
```

You should have an output similar to:

> rke version v1.4.3

### Start Kubernetes cluster

We first write the cluster configuration file.

```ctr:Workload01
cat << EOF > rancher-cluster.yml
nodes:
  - address: ${vminfo:Workload01:public_ip}
    internal_address: ${vminfo:Workload01:private_ip}
    user: ubuntu
    role: [controlplane,etcd,worker]
addon_job_timeout: 120
EOF
```

Then, we start the cluster.

```ctr:Workload01
rke up --config rancher-cluster.yml
```

### Testing the cluster

We can soft symlink the `kube_config_rancher-cluster.yml` file to our `/home/ubuntu/.kube/config` file so that `kubectl` can interact with our cluster.

```ctr:Workload01
mkdir -p /home/ubuntu/.kube
ln -s /home/ubuntu/kube_config_rancher-cluster.yml /home/ubuntu/.kube/config
```

In order to test that we can properly interact with our cluster, we can execute two commands:

```ctr:Workload01
kubectl get nodes
kubectl get pods --all-namespaces
```
