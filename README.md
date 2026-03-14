# Kubernetes-infra

GitOps-driven Kubernetes platform infrastructure — managed with ArgoCD and Helm.

---

## Bootstrap a New Cluster

### Step 1 — Install ArgoCD
```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
kubectl create namespace argocd
helm install argocd argo/argo-cd --namespace argocd --wait
```

### Step 2 — Create Doppler token secret
> ⚠️ This is the ONLY manual step. Everything else is automated.

```bash
kubectl create namespace external-secrets
kubectl create secret generic doppler-token \
  --from-literal=dopplerToken="<your-doppler-token>" \
  --namespace external-secrets
```

Get your token: **Doppler Dashboard → Project → prod → Access → Service Tokens**

### Step 3 — Apply App of Apps
```bash
kubectl apply -f bootstrap/app-of-apps.yaml
```

ArgoCD takes over from here. Everything deploys automatically. ✅

---

## What Gets Deployed Automatically

| App | Namespace | Purpose |
|---|---|---|
| cert-manager | cert-manager | Automatic TLS certificates |
| external-dns | external-dns | Automatic DNS records via Cloudflare |
| external-secrets | external-secrets | Pull secrets from Doppler |
| eso-config | external-secrets | ClusterSecretStore + ExternalSecrets |
| reflector | reflector | Mirror secrets across namespaces |
| jellyfin | jellyfin | Jellyfin media server |

---

## Repo Structure

```
Kubernetes-infra/
├── argocd-apps/          ← ArgoCD Application manifests (App of Apps reads this)
├── eso-config/           ← Doppler SecretStore + ExternalSecret manifests
├── bootstrap/
│   └── app-of-apps.yaml  ← The one file that triggers everything
└── README.md             ← You are here
```

---

## Connected Repos

| Repo | Purpose |
|---|---|
| [Kubernetes-learning](https://github.com/vjrajendran/Kubernetes-learning) | Helm charts (jellyfin, custom-charts) |
| [Kubernetes-infra](https://github.com/vjrajendran/Kubernetes-infra) | ArgoCD apps, ESO config (this repo) |
