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

1. Open <a href="https://cow-demo.${vminfo:Workload01:public_ip}.sslip.io/" target="_blank">cow-demo.${vminfo:Workload01:public_ip}.sslip.io</a> link
2. In your GitOps repository, update `fleet/cow-demo/fleet.yaml` to have 2 cows in green, commit the change on your branch
3. Go back to the cow web application and look at what happens

### More serious application

ℹ _We'll be using a codebase made specifically for this workshop_

1. Open the <a href="https://github.com/devpro/sales-portal" target="_blank">codebase</a> and check the lifecycle management: GitHub PR, GitHub Actions + security checks, SonarCloud, DockerHub, Helm chart repository
2. In Rancher UI, in your workload cluster, from the left menu click on **Service Discovery** > **Ingresses** and look at available Ingresses
3. Open Swagger definition at <a href="https://crm-data.${vminfo:Workload01:public_ip}.sslip.io/swagger" target="_blank">crm-data.${vminfo:Workload01:public_ip}.sslip.io</a> link to add some data
4. Click on the <a href="https://sales-portal.${vminfo:Workload01:public_ip}.sslip.io/" target="_blank">sales-portal.${vminfo:Workload01:public_ip}.sslip.io</a> link to view the web app

## Going further

### Certificate generation with Let's Encrypt

- Add `fleet/letsencrypt` in `system` GitRepo
- Update `cert-manager.io/cluster-issuer` with `letsencrypt-prod` in container definitions (instead of `selfsigned-cluster-issuer`)
- Open applications and check the certificates

### Observability with OpenTelemetry

- Add `fleet/otel-collector` in `system` GitRepo
- Update `enableOpenTelemetry` to `true` in container definitions
- Look at the collector pod logs

### Security with NeuVector

- Add `fleet/neuvector` in `system` GitRepo
- Open <a href="https://neuvector.${vminfo:Workload01:public_ip}.sslip.io/" target="_blank">neuvector.${vminfo:Workload01:public_ip}.sslip.io</a>
- Enter `admin` as username and password, check "I have read and agree to the End User License Agreement" and click on **Login**
- Explore the UI

### Update Sales Portal version

- Make a change in the [codebase](https://github.com/devpro/sales-portal/) to trigger the creation of new container images
- Update `tag` with the latest version in container definitions
