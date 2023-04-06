In this scenario, we will be walking through installing components to practice *Continuous Delivery* in a *Cloud Native* environment.

We will be using two virtual machines today, `management01` and `cluster01` which are located in the tabs in the panel to the right. `management01` will run a Kubernetes cluster and Rancher, and `cluster01` will run a Kubernetes cluster and the corresponding user workloads.

Note that there are two separate Kubernetes clusters at play here, the Management Kubernetes Cluster is dedicated to running Rancher, while the Workload Cluster is managed by Rancher and runs on a separate virtual machine.

**Important Note:** HobbyFarm will tear down your provisioned resources within 10 minutes if your laptop goes to sleep or you navigate off of the HobbyFarm page. Please ensure that you do not do this, for example, during lunch or you will need to restart your scenario.

There is Pause/Resume functionality built into HobbyFarm that will allow you to pause your scenario should you have to put your laptop to sleep temporarily. Please note that pausing the scenario will not extend the end of your resources beyond this Rodeo session.
