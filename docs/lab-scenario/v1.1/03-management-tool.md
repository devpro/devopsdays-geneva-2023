## Installing Helm

Helm is a very popular package manager for Kubernetes.

```ctr:Management01
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 \
  | bash
```

After a successful installation of Helm, we should check our installation.

```ctr:Management01
helm version --client
```

We also have to ensure that the Helm CLI can connect to our Kubernetes cluster.

```ctr:Management01
mkdir -p ~/.kube
cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
sudo chown ec2-user:users ~/.kube/config
chmod 600 ~/.kube/config
```

We can check that this works by listing the Helm charts that are already installed in our cluster.

```ctr:Management01
helm ls --all-namespaces
```

## Installing cert-manager

cert-manager is a Kubernetes add-on to automate the management and issuance of TLS certificates from various issuing sources.

First, we'll add the helm repository for Jetstack

```ctr:Management01
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install \
  cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --version v1.7.1 \
  --set installCRDs=true \
  --create-namespace
```

Once the helm chart has installed, you can monitor the rollout status of both `cert-manager` and `cert-manager-webhook`

```ctr:Management01
kubectl rollout status deploy/cert-manager -n cert-manager
kubectl rollout status deploy/cert-manager-webhook -n cert-manager
```

## Installing Rancher

We will now install Rancher in HA mode onto our `Management01` Kubernetes cluster. The following command will add `rancher-stable` as a helm repository.

```ctr:Management01
helm repo add rancher-stable https://releases.rancher.com/server-charts/stable
helm repo update
helm install rancher rancher-stable/rancher \
  --namespace cattle-system \
  --set hostname=rancher.${vminfo:Management01:public_ip}.sslip.io \
  --set replicas=1 \
  --set bootstrapPassword=RancherOnK3s \
  --create-namespace
kubectl rollout status deploy/rancher -n cattle-system
```

## Accessing Rancher

***Note:*** Rancher may not immediately be available at the link below, as it may be starting up still. Please continue to refresh until Rancher is available.

1. Access Rancher Server at <a href="https://rancher.${vminfo:Management01:public_ip}.sslip.io" target="_blank">https://rancher.${vminfo:Management01:public_ip}.sslip.io</a>
2. For this Rodeo, Rancher is installed with a self-signed certificate from a CA that is not automatically trusted by your browser. Because of this, you will see a certificate warning in your browser. You can safely skip this warning. Some Chromium based browsers may not show a skip button. If this is the case, just click anywhere on the error page and type "thisisunsafe" (without quotes). This will force the browser to bypass the warning and accept the certificate. 
3. Enter the password `RancherOnK3s` when prompted for a password.
4. Select **Set a specific password to use**, and submit a simple password in case you get logged out during the scenario.
5. Make sure to agree to the **Terms & Conditions** and then click continue.
6. When prompted, the **Rancher Server URL** should be `rancher.${vminfo:Management01:public_ip}.sslip.io`, which is the hostname you used to access the server.

You will see the Rancher UI, with the `local` cluster in it. The `local` cluster is the cluster where Rancher itself runs, and should not be used for deploying your demo workloads.

In the top left corner of the UI, you can find a "burger menu" button, which opens up the global navigation menu. There you can access global applications and settings. You have quick links to explore all Rancher managed clusters and a way to get back to the Rancher home page.
