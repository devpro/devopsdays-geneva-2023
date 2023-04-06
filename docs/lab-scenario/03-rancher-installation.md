## Install cert-manager

cert-manager is a Kubernetes add-on to automate the management and issuance of TLS certificates from various issuing sources.

```ctr:Rancher01
kubectl create namespace cert-manager
kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v0.15.0/cert-manager.crds.yaml
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager jetstack/cert-manager --namespace cert-manager --version v0.15.0
```

Once the helm chart has installed, you can monitor the rollout status of both `cert-manager` and `cert-manager-webhook`.

```ctr:Rancher01
kubectl -n cert-manager rollout status deploy/cert-manager
```

You should eventually receive output similar to:

> Waiting for deployment "cert-manager" rollout to finish: 0 of 1 updated replicas are available...
> deployment "cert-manager" successfully rolled out
