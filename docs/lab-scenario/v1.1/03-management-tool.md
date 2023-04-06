## Install Helm

Installing Rancher into our new Kubernetes cluster is easily done with Helm. Helm is a very popular package manager for Kubernetes. It is used as the installation tool for Rancher when deploying Rancher onto a Kubernetes cluster. In order to use Helm, we have to download the Helm CLI.

```ctr:Rancher01
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 \
  | bash
```

After a successful installation of Helm, we should check our installation to ensure that we are ready to install Rancher.

```ctr:Rancher01
helm version --client
```

We also have to ensure that the Helm CLI can connect to our Kubernetes cluster. For this, Helm uses standard Kubeconfig files which it looks for in a `KUBECONFIG` environment variable or in a `~/.kube/config` file in the user's home directory.

K3s writes the Kubeconfig of a cluster to `/etc/rancher/k3s/k3s.yaml`.

We can soft symlink the `/etc/rancher/k3s/k3s.yaml` file to our `~/.kube/config` file so that `helm` can interact with our cluster:

```ctr:Rancher01
mkdir -p ~/.kube
ln -s /etc/rancher/k3s/k3s.yaml ~/.kube/config
```

We can check that this works by listing the Helm charts that are already installed in our cluster:

```ctr:Rancher01
helm ls --all-namespaces
```

## Install cert-manager

cert-manager is a Kubernetes add-on to automate the management and issuance of TLS certificates from various issuing sources.

The following set of steps will install cert-manager which will be used to manage the TLS certificates for Rancher.

First, we'll add the helm repository for Jetstack

```ctr:Rancher01
helm repo add jetstack https://charts.jetstack.io
```

Now, we can install cert-manager version `1.7.1`
```ctr:Rancher01
helm install \
  cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --version v1.7.1 \
  --set installCRDs=true \
  --create-namespace
```

Once the helm chart has installed, you can monitor the rollout status of both `cert-manager` and `cert-manager-webhook`

```ctr:Rancher01
kubectl -n cert-manager rollout status deploy/cert-manager
```

You should eventually receive output similar to:

`Waiting for deployment "cert-manager" rollout to finish: 0 of 1 updated replicas are available...`

`deployment "cert-manager" successfully rolled out`

```ctr:Rancher01
kubectl -n cert-manager rollout status deploy/cert-manager-webhook
```

You should eventually receive output similar to:

`Waiting for deployment "cert-manager-webhook" rollout to finish: 0 of 1 updated replicas are available...`

`deployment "cert-manager-webhook" successfully rolled out`

## Install Rancher

We will now install Rancher in HA mode onto our `Rancher01` Kubernetes cluster. The following command will add `rancher-stable` as a helm repository.

```ctr:Rancher01
helm repo add rancher-stable https://releases.rancher.com/server-charts/stable
```

Finally, we can install Rancher using our `helm install` command.

```ctr:Rancher01
helm install rancher rancher-stable/rancher \
  --namespace cattle-system \
  --set hostname=rancher.${vminfo:rancher01:public_ip}.sslip.io \
  --set replicas=1 \
  --set bootstrapPassword=RancherOnK3s \
  --create-namespace
```

## Accessing Rancher

***Note:*** Rancher may not immediately be available at the link below, as it may be starting up still. Please continue to refresh until Rancher is available.

1. Access Rancher Server at <a href="https://rancher.${vminfo:Rancher01:public_ip}.sslip.io" target="_blank">https://rancher.${vminfo:Rancher01:public_ip}.sslip.io</a>
2. For this Rodeo, Rancher is installed with a self-signed certificate from a CA that is not automatically trusted by your browser. Because of this, you will see a certificate warning in your browser. You can safely skip this warning. Some Chromium based browsers may not show a skip button. If this is the case, just click anywhere on the error page and type "thisisunsafe" (without quotes). This will force the browser to bypass the warning and accept the certificate. 
3. Enter the password `RancherOnK3s` when prompted for a password.
4. Select **Set a specific password to use**, and submit a simple password in case you get logged out during the scenario.
5. Make sure to agree to the **Terms & Conditions** and then click continue.
6. When prompted, the **Rancher Server URL** should be `rancher.${vminfo:Rancher01:public_ip}.sslip.io`, which is the hostname you used to access the server.

You will see the Rancher UI, with the `local` cluster in it. The `local` cluster is the cluster where Rancher itself runs, and should not be used for deploying your demo workloads.

In the top left corner of the UI, you can find a "burger menu" button, which opens up the global navigation menu. There you can access global applications and settings. You have quick links to explore all Rancher managed clusters and a way to get back to the Rancher home page.
