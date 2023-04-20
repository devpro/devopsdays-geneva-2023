## Initiate GitOps git repository

ℹ _We need a codebase to store the component definitions, defined in YAML files, following Helm chart convention_

Fork the example repository either on [GitHub](https://github.com/devpro/devopsdays-geneva-2023) or [GitLab](https://gitlab.com/devpro-labs/devopsdays-geneva-2023).

Go to the repository and create a branch with an explicit name, like `release/lab`.

## Update GitOps definitions

ℹ _We'll use Fleet as GitOps tool but the same logic applies for other GitOps like ArgoCD or Flux_

Review all the files in `fleet` folder and look for **TODO** to be completed before moving on.

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
   - **Paths**: Click on **Add Path** as many time as needed to have: `fleet/crds`
5. Click on **Next**
6. Once the repository has been synchronized, go to **Apps** > **Charts**. There you will now see several new apps that you can install.

### System

Do the same as previous but with the name `system` and the paths: `fleet/ingress-nginx`, `fleet/cert-manager`, `fleet/letsencrypt`, `fleet/nfs-server-provisioner`

### Applications

Do the same as previous but with the name `applications` and the paths: `fleet/cow-demo`, `fleet/salesportal`
