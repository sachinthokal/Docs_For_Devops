# Helm Architecture & Templating Engine 🏛️

Detailed breakdown of Helm v3 architecture, Chart release management, and Go template execution.

---

## 1. High-Level Architecture (Helm v3)

Unlike Helm v2, **Helm v3 is completely client-side** (Tiller component was removed for security reasons). It interacts directly with the Kubernetes API Server using local kubeconfig credentials.

```
+-----------------------------------------------------------------------------------+
|                                HELM CLIENT (CLI)                                  |
|                                                                                   |
|  - Reads Chart Templates & `values.yaml`                                          |
|  - Compiles Go Template Engine into valid Kubernetes Manifests                   |
|  - Uses local `~/.kube/config` credentials                                        |
+----------------------------------------+------------------------------------------+
                                         |
                                         | gRPC / REST API Calls
                                         v
+-----------------------------------------------------------------------------------+
|                           KUBERNETES API SERVER                                   |
|                                                                                   |
|  - Installs / Upgrades / Deletes Resources                                        |
|  - Stores Release History as Secrets in target Namespace (`sh.helm.release.v1.*`) |
+-----------------------------------------------------------------------------------+
```

---

## 2. Release Tracking Engine

Helm stores full release state and revision history inside **Kubernetes Secrets** within the target namespace where the application is deployed.

This architecture enables:
- Concurrent deployment safety.
- Versioned release history (`Revision 1`, `Revision 2`).
- Easy rollbacks using cluster state data.