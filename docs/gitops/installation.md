# GitOps Tooling Installation Guide ⚙️

Step-by-step installation guide for **ArgoCD** on a Kubernetes cluster.

---

## 1. Installing ArgoCD on Kubernetes

```bash
# Create dedicated namespace
kubectl create namespace argocd

# Apply official ArgoCD manifest installation
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Verify all pods are running
kubectl get pods -n argocd
```

---

## 2. Accessing ArgoCD UI & Retrieving Admin Password

```bash
# Port-forward UI to localhost
kubectl port-forward svc/argocd-server -n argocd 8080:443

# Retrieve initial admin auto-generated password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```
*Access UI in browser at `https://localhost:8080` (Username: `admin`).*

---

## 3. Installing ArgoCD CLI (`argocd`)

```bash
# Linux
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd

# macOS
brew install argocd
```