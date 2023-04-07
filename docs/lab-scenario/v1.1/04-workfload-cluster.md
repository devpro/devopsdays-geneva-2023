## Creating a Kubernetes Lab Cluster within Rancher

In this step, we will be creating a Kubernetes Lab environment within Rancher. Normally, in a production case, you would create a Kubernetes Cluster with multiple nodes; however, with this lab environment, we will only be using one virtual machine for the cluster.

1. Go back to the Rancher Home Page
2. On top of the list of available clusters, click **Create**
    We will be using RKE2 cluster, so make sure to switch the toggle to RKE2/K3s
    Note the multiple types of Kubernetes cluster Rancher supports. We will be using **Custom cluster on existing nodes** for this lab, but there are a lot of possibilities with Rancher
3. Click on the **Custom** Cluster box in the **Use existing nodes and create a cluster using RKE2** section
4. Enter a name in the **Cluster Name** box
5. Set the Kubernetes Version to a `v1.22` version
6. All other settings can be kept as default
7. Click **Create** at the bottom
8. Once the cluster is created, you can retrieve an installation command in the **Registration** tab that you can use to add new nodes to your Kubernetes cluster
9. Make sure the boxes **etcd**, **Control Plane**, and **Worker** are all ticked
10. Click **Show Advanced** to the bottom right of the checkboxes
11. Enter the **Node Public IP** (`${vminfo:Workload01:public_ip}`) and **Node Private IP** (`${vminfo:Workload01:private_ip}`)
    - **IMPORTANT:** It is VERY important that you use the correct External and Internal addresses from the **Workload01** machine for this step, and run it on the correct machine. Failure to do this will cause the future steps to fail
12. Check the checkbox to **skip the TLS verification and accept insecure certificates** below the registration command
13. Double click the registration command to copy it to your clipboard
14. Proceed to the next step of this scenario

## Start the Rancher Kubernetes Cluster Bootstrapping Process

**IMPORTANT NOTE:** Make sure you have selected the `Workload01` tab in HobbyFarm in the window to the right. If you run this command on `Management01` you will cause problems for your scenario session

1. Take the copied command and run it on `Workload01`
2. You can follow the provisioning process in the **Machine Pools**, **Conditions** and **Related Resources** tabs
3. Your cluster state in the cluster list and on the cluster detail page will change to **Active**
4. Once your cluster has gone to **Active** you can start exploring it by either clicking the **Explore** button in the cluster list on the home page, or by selecting the cluster in the global menu

## Interacting with the Kubernetes Cluster

In this step, we will be showing basic interaction with our Kubernetes cluster

1. Click into your newly `active` cluster
2. Note the diagrams dials, which illustrate cluster capacity, and the box that show you the recent events in your cluster
3. Click the `Kubectl Shell` button (the button with the Prompt icon) in the top right corner of the Cluster Explorer, and enter `kubectl get pods --all-namespaces` and observe the fact that you can interact with your Kubernetes cluster using `kubectl`
4. Also take note of the `Download Kubeconfig File` button next to it which will generate a Kubeconfig file that can be used from your local desktop or within your deployment pipelines
5. In the left menu, you have access to all Kubernetes resources, the Rancher Application Marketplace and additional cluster tools
