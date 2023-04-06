## Creating A Kubernetes Lab Cluster within Rancher

In this step, we will be creating a Kubernetes Lab environment within Rancher. Normally, in a production case, you would create a Kubernetes Cluster with multiple nodes; however, with this lab environment, we will only be using one virtual machine for the cluster.

1. Hover over the top left dropdown, then click **Global**
    - *The current context is shown in the upper left, and should say 'Global'*
1. Click **Add Cluster**
	- Note the multiple types of Kubernetes cluster Rancher supports. We will be using **Existing nodes** for this lab, but there are a lot of possibilities with Rancher.
1. Click on the **Existing nodes** Cluster box
1. Enter a name in the **Cluster Name** box 
1. Set the Kubernetes Version to a `v1.19` version
1. Click **Next** at the bottom.
1. Make sure the boxes **etcd**, **Control Plane**, and **Worker** are all ticked.
1. Click **Show advanced options** to the bottom right of the **Worker** checkbox
1. Enter the **Public Address** (`${vminfo:Cluster01:public_ip}`) and **Internal Address** (`${vminfo:Cluster01:private_ip}`)
    - **IMPORTANT:** It is VERY important that you use the correct External and Internal addresses from the **Cluster01** machine for this step, and run it on the correct machine. Failure to do this will cause the future steps to fail.
1. Click the clipboard to **Copy to Clipboard** the `docker run` command
1. Proceed to the next step of this scenario

## Start the Rancher Kubernetes Cluster Bootstrapping Process

**IMPORTANT NOTE:** Make sure you have selected the `Cluster01` tab in HobbyFarm in the window to the right. If you run this command on `Rancher01` you will cause problems for your scenario session.

1. Take the copied docker command and run it on `Cluster01`
1. Once the `docker run` command is complete, you should see a message similiar to `1 node has registered`
2. Within the Rancher UI click on `<YOUR_CLUSTER_NAME>` which is the name you entered during cluster creation.
3. You can watch the state of the cluster as your Kubernetes node `Cluster01` registers with Rancher here as well as the **Nodes** tab
4. Your cluster state on the Global page will change to **Active**
5. Once your cluster has gone to **Active** you can click on it and start exploring.

## Interacting with the Kubernetes Cluster

In this step, we will be showing basic interaction with our Kubernetes cluster.

1. Click into your newly `active` cluster. 
2. Note the three dials, which illustrate cluster capacity.
3. Click the `Launch kubectl` button in the top right corner of the Cluster overview page, and enter `kubectl get pods --all-namespaces` and observe the fact that you can interact with your Kubernetes cluster using `kubectl`.
4. Also take note of the `Download Kubeconfig File` button next to it which will generate a Kubeconfig file that can be used from your local desktop or within your deployment pipelines.
5. Click the Ellipses in the top right corner and note the various operational options available for your cluster. We will be exploring these in a later step.
