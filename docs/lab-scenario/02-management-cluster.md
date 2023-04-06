We'll be working on `management01` machine to create a Kubernetes cluster.

## Generate an SSH Keypair

The following command will generate the SSH keypair and copy it into the authorized_keys file, so that we can do SSH tunneling later on.

```ctr:Management01
ssh-keygen -b 2048 -t rsa -f /home/ubuntu/.ssh/id_rsa -N ""
cat /home/ubuntu/.ssh/id_rsa.pub >> /home/ubuntu/.ssh/authorized_keys
```

## Install kubectl

Kubectl is needed to interact with Kubernetes.

```ctr:Management01
sudo curl -L https://dl.k8s.io/release/v1.26.3/bin/linux/amd64/kubectl -o /usr/local/bin/kubectl
sudo chmod +x /usr/local/bin/kubectl
```

We can test `kubectl` and make sure it is properly installed.

```ctr:Management01
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

```ctr:Management01
sudo wget -O /usr/local/bin/rke https://github.com/rancher/rke/releases/download/v1.4.3/rke_linux-amd64
sudo chmod +x /usr/local/bin/rke
```

Validate that RKE CLI is installed properly.

```ctr:Management01
rke -v
```

You should have an output similar to:

> rke version v1.4.3

## Start Kubernetes cluster

We first write the cluster configuration file.

```ctr:Management01
cat << EOF > rancher-cluster.yml
nodes:
  - address: ${vminfo:Management01:public_ip}
    internal_address: ${vminfo:Management01:private_ip}
    user: ubuntu
    role: [controlplane,etcd,worker]
addon_job_timeout: 120
EOF
```

Then, we start the cluster.

```ctr:Management01
rke up --config rancher-cluster.yml
```

## Testing the cluster

We can soft symlink the `kube_config_rancher-cluster.yml` file to our `/home/ubuntu/.kube/config` file so that `kubectl` can interact with our cluster.

```ctr:Management01
mkdir -p /home/ubuntu/.kube
ln -s /home/ubuntu/kube_config_rancher-cluster.yml /home/ubuntu/.kube/config
```

In order to test that we can properly interact with our cluster, we can execute two commands:

```ctr:Management01
kubectl get nodes
kubectl get pods --all-namespaces
```
