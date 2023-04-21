## Installing Helm

ℹ _[Helm](https://helm.sh/) is a very popular package manager for Kubernetes_

We'll install Helm CLI locally:

```ctr:Management01
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 \
  | bash
```

After a successful installation of Helm, we should check our installation:

```ctr:Management01
helm version --client
```

We also have to ensure that the Helm CLI can connect to our Kubernetes cluster:

```ctr:Management01
mkdir -p ~/.kube
cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
sudo chown ec2-user:users ~/.kube/config
chmod 600 ~/.kube/config
```

We can check that this works by listing the Helm charts that are already installed in our cluster:

```ctr:Management01
helm ls --all-namespaces
```

## Installing cert-manager

ℹ _[cert-manager](https://cert-manager.io/) is a Kubernetes add-on to automate the management and issuance of TLS certificates from various issuing sources_

We'll install cert-manager with Helm:

```ctr:Management01
helm repo add jetstack https://charts.jetstack.io
helm repo update
kubectl apply -f \
  https://github.com/cert-manager/cert-manager/releases/download/v1.11.0/cert-manager.crds.yaml
helm upgrade --install cert-manager jetstack/cert-manager \
  --namespace cert-manager --create-namespace \
  --version v1.11.0
```

Once the helm chart has installed, you can monitor the rollout status of both deployments:

```ctr:Management01
kubectl rollout status deploy/cert-manager -n cert-manager
kubectl rollout status deploy/cert-manager-webhook -n cert-manager
```

## Installing Rancher

ℹ _[Rancher](https://www.rancher.com/) is an open source solution to manage Kubernetes clusters_

We'll install Rancher with Helm:

```ctr:Management01
helm repo add rancher-stable https://releases.rancher.com/server-charts/stable
helm repo update
helm upgrade --install rancher rancher-stable/rancher \
  --namespace cattle-system --create-namespace \
  --set hostname=rancher.${vminfo:Management01:public_ip}.sslip.io \
  --set replicas=1 \
  --set bootstrapPassword=RancherOnK3s
```

Once the helm chart has installed, you can monitor the rollout status of `rancher` deployment:

```ctr:Management01
kubectl rollout status deploy/rancher -n cattle-system
```

## Accessing Rancher

⚠ Rancher may not immediately be available at the link below, as it may be starting up still, so refresh until it does

1. Access Rancher Server at <a href="https://rancher.${vminfo:Management01:public_ip}.sslip.io" target="_blank">https://rancher.${vminfo:Management01:public_ip}.sslip.io</a>
2. As Rancher is installed with a self-signed certificate from a CA that is not automatically trusted by your browser, you will see a certificate warning in your browser and you can safely skip this warning
3. Enter the password `RancherOnK3s` when prompted for a password
4. Check **End User License Agreement & Terms & Conditions** and then click **Continue**

## Rancher UI

⚠ To avoid potential UI issues, you can deactivate protections (such as DuckDuckGo Chrome extension)

You will see the `local` cluster. The `local` cluster is the cluster where Rancher itself runs, and should not be used for deploying your demo workloads.
