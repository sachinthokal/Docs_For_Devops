# GitOps Commands Cheat Sheet (`argocd` & `flux`) ⚡

Categorized command sheet for managing ArgoCD applications, sync status, and Flux reconciliation.

---

## 1. ArgoCD CLI Commands (`argocd`)

```bash
# Login to ArgoCD server
argocd login localhost:8080 --username admin --password <password> --insecure

# Create a GitOps Application
argocd app create guestbook   --repo https://github.com/argoproj/argocd-example-apps.git   --path guestbook   --dest-server https://kubernetes.default.svc   --dest-namespace default

# List all applications and their health status
argocd app list

# Sync application (Apply Git desired state to live cluster)
argocd app sync guestbook

# Enable Automated Sync & Self-Healing
argocd app set guestbook --sync-policy automated --auto-prune --self-heal
```

---

## 2. Sample ArgoCD Application Manifest (`app.yaml`)

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: web-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: 'https://github.com/my-org/k8s-manifests.git'
    targetRevision: HEAD
    path: overlays/production
  destination:
    server: 'https://kubernetes.default.svc'
    namespace: prod
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```