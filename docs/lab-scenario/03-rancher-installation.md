We'll be working on `management01` machine to install Rancher that will help us manage the Kubernetes clusters.

## Install cert-manager

[cert-manager](https://cert-manager.io/) automates the management and issuance of TLS certificates from various issuing sources.

```ctr:Management01
# https://cert-manager.io/docs/installation/helm/
helm repo add jetstack https://charts.jetstack.io
helm repo update
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.11.0/cert-manager.crds.yaml
helm install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace --version v1.11.0
```

Once the helm chart is installed, monitor the rollout status of the deployments.

```ctr:Management01
kubectl -n cert-manager rollout status deploy/cert-manager
kubectl -n cert-manager-webhook rollout status deploy/cert-manager
```

You should eventually receive output similar to:

> Waiting for deployment "cert-manager" rollout to finish: 0 of 1 updated replicas are available...
> deployment "cert-manager" successfully rolled out

## Install Rancher

Rancher is an open source solution to manage Kubernetes clusters.

```ctr:Management01
helm repo add rancher-stable https://releases.rancher.com/server-charts/stable
helm repo update
kubectl create namespace cattle-system
helm upgrade --install rancher rancher-stable/rancher \
  --set hostname=rancher.${vminfo:management01:public_ip}.sslip.io \
  --set replicas=1 \
  --namespace cattle-system
```

Before we access Rancher, we need to make sure that `cert-manager` has signed a certificate using the `cattle-ca` in order to make sure our connection to Rancher does not get interrupted. The following bash script will check for the certificate we are looking for.

```ctr:Management01
while true; do curl -kv https://rancher.${vminfo:Management01:public_ip}.sslip.io 2>&1 | grep -q "dynamiclistener-ca"; if [ $? != 0 ]; then echo "Rancher isn't ready yet"; sleep 5; continue; fi; break; done; echo "Rancher is Ready";
```

## Accessing Rancher

***Note:*** Rancher may not immediately be available at the link below, as it may be starting up still. Please continue to refresh until Rancher is available.

1. Access Rancher Server at <a href="https://rancher.${vminfo:Management01:public_ip}.sslip.io" target="_blank">https://rancher.${vminfo:Management01:public_ip}.sslip.io</a>
2. Enter a password for the default `admin` user when prompted.
3. Select the default view of _"I want to create or manage multiple clusters"_
4. Make sure to agree to the Terms & Conditions
5. When prompted, the **Rancher Server URL** should be `rancher.${vminfo:Management01:public_ip}.sslip.io`, which is the hostname you used to access the server.
6. Once you log in, you'll see a message similar to "Waiting for server-url to be set". Click the ellipses on the right of the local cluster, click Edit, then click Save.

You will see the Rancher UI, with the `local` cluster in it. The `local` cluster is the cluster where Rancher itself runs, and should not be used for deploying your demo workloads.
