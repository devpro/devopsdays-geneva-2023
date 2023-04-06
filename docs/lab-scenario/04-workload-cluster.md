## Creating a Kubernetes Lab Cluster within Rancher

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

## Start the Rancher Kubernetes Cluster Bootstrapping Process

**IMPORTANT NOTE:** Make sure you have selected the `Workload01` tab in HobbyFarm in the window to the right. If you run this command on `Rancher01` you will cause problems for your scenario session.

1. Take the copied docker command and run it on `Workload01`
2. Once the `docker run` command is complete, you should see a message similiar to `1 node has registered`
3. Within the Rancher UI click on `<YOUR_CLUSTER_NAME>` which is the name you entered during cluster creation.
4. You can watch the state of the cluster as your Kubernetes node `Workload01` registers with Rancher here as well as the **Nodes** tab
5. Your cluster state on the Global page will change to **Active**
6. Once your cluster has gone to **Active** you can click on it and start exploring.

## Interacting with the Kubernetes Cluster

In this step, we will be showing basic interaction with our Kubernetes cluster.

1. Click into your newly `active` cluster.
2. Note the three dials, which illustrate cluster capacity.
3. Click the `Launch kubectl` button in the top right corner of the Cluster overview page, and enter `kubectl get pods --all-namespaces` and observe the fact that you can interact with your Kubernetes cluster using `kubectl`.
4. Also take note of the `Download Kubeconfig File` button next to it which will generate a Kubeconfig file that can be used from your local desktop or within your deployment pipelines.
5. Click the Ellipses in the top right corner and note the various operational options available for your cluster. We will be exploring these in a later step.
