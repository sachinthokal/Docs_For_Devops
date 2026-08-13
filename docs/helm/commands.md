# Helm Commands & Template Cheat Sheet ⚡

Categorized command sheet for chart installation, releases, repository management, and custom values.

---

## 1. Essential Helm Commands

| Command | Description | Example |
| :--- | :--- | :--- |
| `helm create <name>` | Scaffold a new chart directory structure. | `helm create my-app` |
| `helm install` | Install a chart onto Kubernetes cluster. | `helm install my-web bitnami/nginx` |
| `helm upgrade` | Upgrade an existing release. | `helm upgrade my-web bitnami/nginx` |
| `helm rollback` | Roll back release to a previous revision. | `helm rollback my-web 1` |
| `helm list` | List all active releases in current namespace. | `helm list -A` |
| `helm uninstall` | Delete release and all its resources. | `helm uninstall my-web` |

---

## 2. Using Custom Values

```bash
# Override values via CLI flags
helm install my-web bitnami/nginx --set service.type=NodePort --set replicaCount=3

# Override values using a custom values YAML file
helm install my-web bitnami/nginx -f production-values.yaml

# Dry run & render templates to stdout without applying to cluster
helm template my-web ./mychart -f values.yaml
```

---

## 3. Sample Helm Template Snippet (`deployment.yaml`)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-app
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app: {{ .Release.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          ports:
            - containerPort: {{ .Values.service.port }}
```