## Generate an SSH Keypair

The following command will generate the SSH keypair and copy it into the authorized_keys file.

```ctr:Rancher01
ssh-keygen -b 2048 -t rsa -f /home/ubuntu/.ssh/id_rsa -N ""
cat /home/ubuntu/.ssh/id_rsa.pub >> /home/ubuntu/.ssh/authorized_keys
```

## Download RKE

Rancher Kubernetes Engine (RKE) is an extremely simple, lightning fast Kubernetes installer that works everywhere.

In this step, we will download the `RKE CLI` to the `Rancher01` node.

```ctr:Rancher01
sudo wget -O /usr/local/bin/rke https://github.com/rancher/rke/releases/download/v1.2.1/rke_linux-amd64
sudo chmod +x /usr/local/bin/rke
```

Next, let's validate that RKE is installed properly:

```ctr:Rancher01
rke -v
```

You should have an output similar to:

> rke version v1.2.1

## Install kubectl

In order to interact with our Kubernetes cluster, we need to install `kubectl`.

```ctr:Rancher01
sudo curl -L https://dl.k8s.io/release/v1.21.0/bin/linux/amd64/kubectl -o /usr/local/bin/kubectl
sudo chmod +x /usr/local/bin/kubectl
```

We can test `kubectl` and make sure it is properly installed.

```ctr:Rancher01
kubectl version --client
```

## Install Helm

Helm 3 is a very popular package manager for Kubernetes. It is used as the installation tool for Rancher when deploying Rancher onto a Kubernetes cluster. In order to download Helm, we need to download the Helm tar.gz, move it into the appropriate directory, and mark it as executable. Finally, we will clean up the helm artifacts that are not necessary.

```ctr:Rancher01
sudo wget -O helm.tar.gz https://get.helm.sh/helm-v3.4.0-linux-amd64.tar.gz
sudo tar -zxf helm.tar.gz
sudo mv linux-amd64/helm /usr/local/bin/helm
sudo chmod +x /usr/local/bin/helm
sudo rm -rf linux-amd64
sudo rm -f helm.tar.gz
```

After a successful installation of Helm, we should check our installation to ensure that we are ready to install Rancher.

```ctr:Rancher01
helm version --client
```

## Create a rancher-cluster.yml file

RKE CLI uses a YAML-formatted file to describe the configuration of our cluster. The following command will heredoc write the corresponding file onto your `rancher01` node, so that `RKE` will be able to install Kubernetes.

`RKE` uses SSH tunneling, which is why we generated the keypair in the first part of this scenario.

```ctr:Rancher01
cat << EOF > rancher-cluster.yml
nodes:
  - address: ${vminfo:Rancher01:public_ip}
    internal_address: ${vminfo:Rancher01:private_ip}
    user: ubuntu
    role: [controlplane,etcd,worker]
addon_job_timeout: 120
EOF
```

## Run rke up

We are now ready to run `rke up` to install Kubernetes onto our `Rancher01` node.

The following command will run `rke up` which will install Kubernetes onto our node.

```ctr:Rancher01
rke up --config rancher-cluster.yml
```

## Testing your cluster

RKE will have generated two important files:

* `kube_config_rancher-cluster.yml`
* `rancher-cluster.clusterstate`

in addition to your

* `rancher-cluster.yml`

All of these files are extremely important for future maintenance of our cluster. When running `rke` on your own machines to install Kubernetes/Rancher, you must make sure you have current copies of all 3 files otherwise you can run into errors when running `rke up`.

The `kube_config_rancher-cluster.yml` file will contain a `kube-admin` kubernetes context that can be used to interact with your Kubernetes cluster that you've installed Rancher on. 

We can soft symlink the `kube_config_rancher-cluster.yml` file to our `/home/ubuntu/.kube/config` file so that `kubectl` can interact with our cluster:

```ctr:Rancher01
mkdir -p /home/ubuntu/.kube
ln -s /home/ubuntu/kube_config_rancher-cluster.yml /home/ubuntu/.kube/config
```

In order to test that we can properly interact with our cluster, we can execute two commands: 

```ctr:Rancher01
kubectl get nodes
```

```ctr:Rancher01
kubectl get pods --all-namespaces
```
