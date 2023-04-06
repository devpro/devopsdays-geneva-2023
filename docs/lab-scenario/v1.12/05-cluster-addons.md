## Enable Rancher Monitoring

To deploy the _Rancher Monitoring_ feature, we will need to navigate to the Cluster Explorer.

1. On your newly-created cluster, click the "Explorer" button to open the Cluster Explorer.
2. Once the Cluster Explorer loads, use the dropdown in the upper-left section of the page, and navigate to "Apps & Marketplace."
3. Locate the "Monitoring" chart, and click on it
4. Select "Chart Options" on the left. Change Resource Limits > Requested CPU from `750m` to `250m`. This is required because our scenario virtual machine has limited CPU available.
5. Click "Install" at the bottom of the page, and wait for the `helm` install operation to complete.

Once Monitoring has been installed, you can click on that application under "Installed Apps" to view the various resources that were deployed.

## Working with Rancher Monitoring

Once Rancher Monitoring has been deployed, we can view the various components and interact with them. 

1. In the dropdown in the upper-left corner of the Cluster Explorer, select "Monitoring"
2. On the Monitoring Dashboard page, identify the "Grafana" link. Clicking this will proxy you to the installed Grafana server

Once you have opened Grafana, feel free to explore the various dashboard and visualizations that have been setup by default. 

These options can be customized (metrics and graphs), but doing so is out of the scope of this scenario. 

## Create a Deployment And Service

In this step, we will be creating a Kubernetes Deployment and Kubernetes Service for an arbitrary workload. For the purposes of this lab, we will be using the docker image `rancher/hello-world:latest` but you can use your own docker image if you have one for testing.

When we deploy our container in a pod, we probably want to make sure it stays running in case of failure or other disruption. Pods by nature will not be replaced when they terminate, so for a web service or something we intend to be always running, we should use a Deployment.

The deployment is a factory for pods, so you'll notice a lot of similairities with the Pod's spec. When a deployment is created, it first creates a replica set, which in turn creates pod objects, and then continues to supervise those pods in case one or more fails.

_Note: These steps will need to be executed within the Cluster Manager. To access, click on the Cluster Manager button at the top of the page._

---
1. Hover over the Dropdown next to the Rancher logo in the top left corner, hover over your cluster name, then select **Default** as the project.
1. Under the **Workloads** tab press **Deploy** in the top right corner and enter the following criteria:
	- **Name** - `helloworld`
	- **Docker Image** - `rancher/hello-world:latest`
	- Click **Add Port** and enter `80` for the container port
	- ** NOTE: ** Note the other capabilities you have for deploying your container. We won't be covering these in this Rodeo, but you have plenty of capabilities here.
1. Scroll down and click **Launch**
1. You should see one pod get deployed and a TCP endpoint under your workload name exposing the NodePort service that has been created.
1. Click the Arrow next to your workload that you just created, then note the `+` and `-` buttons under the replica count to the right. You can click these correspondingly and refresh your browser on the nodeport to see the changes in the pod name.

## Create a Kubernetes Ingress

In this step, we will be creating a Layer 7 ingress to access the workload we just deployed in the previous step. For this example, we will be using [sslip.io](http://sslip.io/) as a way to provide a DNS hostname for our workload. Rancher will automatically generate a corresponding workload IP.

1. Hover over the Dropdown next to the Rancher logo in the top left corner, hover over your cluster name, then select **Default** as the project.
1. In the **Default** project in the **Workloads** section, click on the **Load Balancing** tab
1. Click **Add Ingress** and enter the following criteria:
	- **Name** - `helloworld`
	- Leave '**Automatically generate a `.sslip.io` hostname**' selected
	- Click the minus button on the right to remove the empty backend rule
	- Click the **+ Service** button
	- Pick the `helloworld-nodeport` service from the dropdown under **Target**
1. Click **Save** and wait for the `sslip.io` hostname to register, you should see the rule become **Active** within a few minutes.
1. Click on the hostname and browse to the workload.

** Note: ** You may receive transient 404/502/503 errors while the workload stabilizes. This is due to the fact that we did not set a proper readiness probe on the workload, so Kubernetes is simply assuming the workload is healthy.

## Upgrading your Kubernetes Cluster

This step shows how easy it is to upgrade your Kubernetes clusters within Rancher.

1. Hover over the Dropdown next to the Rancher logo in the top left corner, then select your cluster.
1. Click the ellipses (...) next to the `Kubeconfig file` button
1. Click Edit
1. Scroll down and select the dropdown under the `Kubernetes Version`
1. Select a newer version of Kubernetes
1. Scroll down and hit `Save`

Observe that your Kubernetes cluster will now be upgraded.
