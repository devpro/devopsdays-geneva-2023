## Initiate GitOps repository

ℹ _In a GitOps approach, components (Kubernetes objects) are defined in a git repository, in plain text files (usually YAML)_

We need a cobebase to store our definitions. To start with already defined templates (Helm chart based),
fork the example repository either on [GitHub](https://github.com/devpro/devopsdays-geneva-2023) or [GitLab](https://gitlab.com/devpro-labs/devopsdays-geneva-2023).

> You should now have your own public git repository for this lab (for example `https://github.com/myaccount/devopsdays-geneva-2023.git`)

## Retrieve cluster public IP

ℹ _[sslip.io](http://sslip.io/) is a DNS (Domain Name System) service that provides immediate resolution and avoids DNS update/replication_

We are already using it for the management tools and we'll use it for our workload that is exposed publicly.

> For all our tests we'll use `${vminfo:Workload01:public_ip}.sslip.io` as our main domain (**${vminfo:Workload01:public_ip}** is the public IP for this cluster)

## Update GitOps definitions

ℹ _This lab will use Fleet as GitOps tool but the same logic applies for other GitOps like ArgoCD or Flux_

1. Go to the git repository you just created and create a branch with an explicit name, like `release/lab`
2. Review all the files in `fleet` folder and look for **TODO** to be completed before moving on
   - Replace `W.X.Y.Z` with ${vminfo:Workload01:public_ip}
   - Replace `your@own.email` with your email address

## Create GitOps repositories in Rancher

### Custom Resources

1. From Rancher home page, from the main menu on the left, go to **GLOBAL APPS** > **Continuous Delivery**
2. Click on **Git Repos** in the left menu
3. Click on **Add Repository**
4. Enter the following details:
   - **Name**: `custom-resources`
   - **Repository URL**: git URL of your repository (for example `https://github.com/myaccount/devopsdays-geneva-2023.git`)
   - **Watch**: `A branch`
   - **Branch Name**: your branch name (for example `release/lab`)
   - **Git Authentication**: leave `None` (except if you use a private repository)
   - **Helm Authentication**: leave `None`
   - **TLS Certificate Verification**: leave `Require a valid certificate`
   - **Paths**: `fleet/crds`
5. Click on **Next**
6. Enter the following details:
   - **Target**: Select your cluster
   - **Service Account Name**: Leave empty
   - **Target Namespace**: Leave empty
7. Click on **Create**

> Wait for **Clusters Ready** displays "1/1"

### System

Do the same as previous but with the name `system` and the paths (click on **Add Path** as many time as needed to have): `fleet/cert-manager`, `fleet/nfs-server-provisioner`

> Wait for **Clusters Ready** displays "1/1"

### Applications

Do the same as previous but with the name `applications` and the paths: `fleet/cow-demo`, `fleet/salesportal`

> Wait for **Clusters Ready** displays "1/1"

## Enjoy GitOps

### Play with cows

1. In Rancher UI, in your workload cluster, from the left menu, click on **Service Discovery** > **Ingresses** and look at available Ingresses
2. Click on the <a href="https://cow-demo.${vminfo:Workload01:public_ip}.sslip.io/" target="_blank">cow-demo.${vminfo:Workload01:public_ip}.sslip.io</a> link
3. In your GitOps repository, update `fleet/cow-demo/fleet.yaml` to have 2 cows in green, commit the change on your branch
4. Go back to the cow web application and look at what happens

### More serious application

1. In Rancher UI, in your workload cluster, from the left menu
2. Finally, from the left menu, click on **Service Discovery** > **Ingresses** and look at available Ingresses
3. Click on the <a href="https://sales-portal.${vminfo:Workload01:public_ip}.sslip.io/" target="_blank">cow-demo.${vminfo:Workload01:public_ip}.sslip.io</a> link

## Going further

- Add `fleet/letsencrypt` in `system` GitOps repository and update `cert-manager.io/cluster-issuer` with `letsencrypt-prod` (instead of `selfsigned-cluster-issuer`)
- Add `fleet/otel-collector` in `system` and look at the pod logs
- Add `fleet/neuvector` in `system` and open <a href="https://neuvector.${vminfo:Workload01:public_ip}.sslip.io/" target="_blank">neuvector.${vminfo:Workload01:public_ip}.sslip.io</a>
