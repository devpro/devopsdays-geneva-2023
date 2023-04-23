## Creating a Kubernetes Lab Cluster within Rancher

We will be creating a Kubernetes Lab environment within Rancher on `workload01`:

1. Go Rancher Home Page
2. On top of the list of available clusters, click **Create**
3. Switch the toggle to RKE2/K3s and click on the **Custom** Cluster box in the **Use existing nodes and create a cluster using RKE2** section
4. Enter a name in the **Cluster Name** box, for example `demo`
5. All settings can be kept as default
6. Click **Create** at the bottom
7. Once the cluster object is created, you can retrieve an installation command in the **Registration** tab that you can use to add new nodes to your Kubernetes cluster
8. Make sure the boxes **etcd**, **Control Plane**, and **Worker** are all ticked
9. Click **Show Advanced** to the bottom right of the checkboxes
10. Enter the **Node Public IP** (`${vminfo:Workload01:public_ip}`) and **Node Private IP** (`${vminfo:Workload01:private_ip}`)
    - **IMPORTANT:** It is VERY important that you use the correct External and Internal addresses from the **Workload01** machine for this step, and run it on the correct machine. Failure to do this will cause the future steps to fail
11. Check the checkbox to **Insecure: Select this to skip TLS verification if your server has a self-signed certificate.** below the registration command
12. Click the registration command to copy it to your clipboard

## Start the Rancher Kubernetes Cluster Bootstrapping Process

⚠ Make sure you have selected the `Workload01` tab in HobbyFarm in the window to the right:

1. Take the copied command and run it on `Workload01`
2. You can follow the provisioning process in the **Machines**, **Conditions** and **Related Resources** tabs
3. Your cluster state in the cluster list and on the cluster detail page will change to **Active**
4. Once your cluster has gone to **Active** you can start exploring it by either clicking the **Explore** button in the cluster list on the home page, or by selecting the cluster in the global menu

## Explore the workload cluster

⚠ In the namespace filter at the top, select All Namespaces to see everything.

From the left menu, open the cluster and look at the different informations. In **Apps** > **Installed Apps**, have a look at everything that has been deployed.
