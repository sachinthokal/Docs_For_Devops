# Kubernetes Role-Based Access Control (RBAC): Zero to Hero Guide

## 1. Introduction to RBAC in Kubernetes

### What is RBAC?

**Role-Based Access Control (RBAC)** is a method of regulating access to computer or network resources based on the roles of individual users within an enterprise. In Kubernetes, RBAC is an authorization mechanism that determines whether a user or service account has permission to perform a specific action on a specific resource within the cluster.

### Why RBAC? (Security & Governance)

* **Principle of Least Privilege (PoLP):** Ensure entities (users, microservices, CI/CD pipelines) only have the absolute minimum permissions required to perform their jobs.
* **Blast Radius Reduction:** Prevent compromised credentials or compromised pods from affecting cluster-wide resources or other namespaces.
* **Multi-Tenancy & Isolation:** Safely host multiple teams, environments (Dev, Staging, Prod), and applications on a single Kubernetes cluster.
* **Compliance & Auditing:** Satisfy regulatory requirements (SOC2, PCI-DSS, HIPAA) by maintaining fine-grained control over who did what, where, and when.

---

## 2. Kubernetes Authorization & RBAC Architecture

### 2.1 The Request Lifecycle in API Server

Every request sent to the Kubernetes API Server (`kube-apiserver`) goes through three distinct stages before reaching the storage layer (`etcd`):

```
+---------------------------------------------------------------------------------+
|                                Kubernetes API Server                            |
|                                                                                 |
|  [ Client / kubectl / Pod ]                                                     |
|              |                                                                  |
|              v                                                                  |
|    +-------------------+      +-------------------+      +-------------------+  |
|    |  Authentication   | ---> |   Authorization   | ---> | Admission Control |  |
|    |  (Who are you?)   |      |  (Can you do it?) |      |  (Is it valid?)   |  |
|    +-------------------+      +-------------------+      +-------------------+  |
|              |                          |                          |            |
|              v                          v                          v            |
|      Identifies User/SA         Checks RBAC Rules          Validates/Mutates    |
|      (e.g., jane, dev-sa)       (Role, ClusterRole)        (Limits, PSP/PSS)   |
|                                                                    |            |
+--------------------------------------------------------------------+------------+
                                                                     |
                                                                     v
                                                             +---------------+
                                                             |     etcd      |
                                                             +---------------+
```

1. **Authentication (AuthN):** Validates *who* is making the request using Client Certificates, Bearer Tokens, OpenID Connect (OIDC), or Webhooks.
2. **Authorization (AuthZ):** Evaluates *if* the authenticated entity has permission to execute the requested action (`get`, `create`, `delete`, etc.) on the target resource (`pods`, `services`, `secrets`). **This is where RBAC operates.**
3. **Admission Control:** Mutates or validates the request before persisting it to `etcd` (e.g., checking ResourceQuotas, injecting sidecars).

---

### 2.2 Core Concepts & API Primitives

Kubernetes RBAC consists of four core API objects, split into two categories: **Roles** (rules) and **Bindings** (associations).

```
   PERMISSIONS (What can be done)               SUBJECTS (Who does it)
+-----------------------------------+       +----------------------------+
|  Role (Namespaced)                |       |  User Account (Human)      |
|  ClusterRole (Cluster-wide)       |       |  Group (Collection)        |
+-----------------------------------+       |  ServiceAccount (Pod/App)  |
                  ^                         +----------------------------+
                  |                                       ^
                  +-------------- Binding ----------------+
                       (RoleBinding / ClusterRoleBinding)
```

#### 1. Verbs (Actions)

Verbs represent the actions a subject can perform:

* `get`, `list`, `watch` (Read operations)
* `create`, `update`, `patch`, `delete`, `deletecollection` (Write operations)
* `use` (Special verb for PodSecurityPolicies or PodSecurityStandards)

#### 2. Resources (Targets)

The target Kubernetes API objects, such as `pods`, `services`, `deployments`, `configmaps`, `secrets`, `nodes`, `namespaces`.

* Subresources can also be targeted (e.g., `pods/log`, `pods/exec`, `deployments/scale`).

#### 3. API Groups

Kubernetes APIs are grouped logically:

* Core Group: `""` (empty string for `pods`, `services`, `namespaces`, `configmaps`, `secrets`)
* Named Groups: `apps` (`deployments`, `statefulsets`), `batch` (`jobs`, `cronjobs`), `rbac.authorization.k8s.io` (`roles`, `rolebindings`).

---

### 2.3 Object Matrix: Namespaced vs Cluster-Wide

| Feature | Role | ClusterRole |
| :--- | :--- | :--- |
| **Scope** | Single Namespace | Cluster-wide (or bindable to namespaces) |
| **API Version** | `rbac.authorization.k8s.io/v1` | `rbac.authorization.k8s.io/v1` |
| **Target Resources** | Namespaced resources (`pods`, `services`, `configmaps`) | Cluster-wide resources (`nodes`, `pv`, `namespaces`) OR namespaced resources across all namespaces |
| **Bound By** | `RoleBinding` | `ClusterRoleBinding` OR `RoleBinding` |
| **Primary Use Case** | Developer access within `dev` namespace | Cluster admin, cross-namespace monitoring, node management |

---

### 2.4 Bindings Matrix: Combinations & Scope Impact

| Role Type | Binding Type | Effective Scope & Behavior |
| :--- | :--- | :--- |
| **Role** | **RoleBinding** | Grants permissions **only** within the namespace where the Role and RoleBinding reside. |
| **ClusterRole** | **ClusterRoleBinding** | Grants permissions across **the entire cluster** (all namespaces + cluster-scoped resources like Nodes/PVs). |
| **ClusterRole** | **RoleBinding** | Grants permissions defined in the ClusterRole, but **restricted strictly** to the namespace of the RoleBinding! (Great for reusability). |
| **Role** | **ClusterRoleBinding** | **INVALID.** A ClusterRoleBinding cannot bind a namespaced `Role`. |

---

## 3. Subjects in Kubernetes: Users vs ServiceAccounts

Kubernetes distinguishes between human users and automated workloads:

```
                                  +------------------------------------+
                                  |            SUBJECTS                |
                                  +------------------------------------+
                                                    |
                      +-----------------------------+-----------------------------+
                      |                                                           |
                      v                                                           v
        +---------------------------+                               +---------------------------+
        |        HUMAN USERS        |                               |     SERVICE ACCOUNTS      |
        +---------------------------+                               +---------------------------+
        | • External to K8s         |                               | • Managed by K8s API      |
        | • No 'User' API resource  |                               | • Namespaced API object   |
        | • Auth via Certs/OIDC/X509|                               | • Used by Pods / Apps     |
        | • Example: developer-jane |                               | • Example: app-sa         |
        +---------------------------+                               +---------------------------+
```

1. **User Accounts (Humans):**
   * Kubernetes **does not** have a `User` API object in `etcd`.
   * Users are managed externally (e.g., X.509 certificates, AWS IAM, Google Workspace, Keycloak via OIDC).
   * Kubernetes trusts the username extracted from authentication credentials (e.g., `CN=jane` in a client cert).

2. **Group Accounts:**
   * Used to assign permissions to multiple users at once (e.g., `O=system:masters` or `O=dev-team` in X.509 certs).

3. **Service Accounts (Applications / Pods):**
   * **Are** actual Kubernetes objects stored in `etcd`.
   * Used by workloads running inside pods to authenticate with `kube-apiserver`.
   * Automatically mounted into pods at `/var/run/secrets/kubernetes.io/serviceaccount/token`.

---

## 4. Deep Dive Manifest Reference

### 4.1 Role (Namespaced)

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: development
  name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["pods", "pods/log"]
  verbs: ["get", "watch", "list"]
- apiGroups: ["apps"]
  resources: ["deployments"]
  verbs: ["get", "list"]
```

### 4.2 ClusterRole (Cluster-Scoped)

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: secret-reader-global
rules:
- apiGroups: [""]
  resources: ["secrets"]
  verbs: ["get", "list", "watch"]
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["get", "list"]
```

### 4.3 RoleBinding

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods-binding
  namespace: development
subjects:
- kind: User
  name: jane
  apiGroup: rbac.authorization.k8s.io
- kind: ServiceAccount
  name: my-app-sa
  namespace: development
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

### 4.4 ClusterRoleBinding

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: read-secrets-global-binding
subjects:
- kind: Group
  name: security-auditors
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: secret-reader-global
  apiGroup: rbac.authorization.k8s.io
```

---

## 5. Step-by-Step Hands-On Tutorial & Installation

### Prerequisites & Setup

Ensure you have access to a local or remote Kubernetes cluster (`minikube`, `kind`, or `EKS`/`GKE`) and `kubectl` configured.

```bash
# Check if RBAC mode is enabled in your cluster
kubectl cluster-info
kubectl api-versions | grep rbac
```

---

### Step 1: Create a Namespace

```bash
kubectl create namespace dev-team
```

---

### Step 2: Create a ServiceAccount

```bash
kubectl create serviceaccount build-robot -n dev-team
```

---

### Step 3: Create a Role (Imperative & Declarative)

**Imperative command:**

```bash
kubectl create role pod-manager   --verb=get,list,watch,create,delete   --resource=pods   --namespace=dev-team
```

**Declarative YAML (`pod-manager-role.yaml`):**

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: dev-team
  name: pod-manager
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch", "create", "delete"]
```

Apply it:

```bash
kubectl apply -f pod-manager-role.yaml
```

---

### Step 4: Bind Role to ServiceAccount

**Imperative command:**

```bash
kubectl create rolebinding build-robot-pod-manager   --role=pod-manager   --serviceaccount=dev-team:build-robot   --namespace=dev-team
```

---

### Step 5: Test & Verify Permissions (`auth can-i`)

Use `kubectl auth can-i` to verify if an action is permitted under a specific user/ServiceAccount context:

```bash
# Can build-robot list pods in dev-team namespace? (Should output: yes)
kubectl auth can-i list pods --as=system:serviceaccount:dev-team:build-robot -n dev-team

# Can build-robot delete deployments in dev-team namespace? (Should output: no)
kubectl auth can-i delete deployments --as=system:serviceaccount:dev-team:build-robot -n dev-team

# Can build-robot list pods in default namespace? (Should output: no)
kubectl auth can-i list pods --as=system:serviceaccount:dev-team:build-robot -n default
```

---

### Step 6: Create a User Certificate & Authenticate

To simulate a real human user (`developer-bob`):

```bash
# 1. Generate Private Key
openssl genrsa -out bob.key 2048

# 2. Create Certificate Signing Request (CSR)
openssl req -new -key bob.key -out bob.csr -subj "/CN=developer-bob/O=developers"

# 3. Create K8s CertificateSigningRequest object
cat <<EOF | kubectl apply -f -
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: developer-bob-csr
spec:
  request: $(cat bob.csr | base64 | tr -d '
')
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth
EOF

# 4. Approve CSR
kubectl certificate approve developer-bob-csr

# 5. Extract signed certificate
kubectl get csr developer-bob-csr -o jsonpath='{.status.certificate}' | base64 --decode > bob.crt

# 6. Add credentials to kubeconfig
kubectl config set-credentials developer-bob   --client-certificate=bob.crt   --client-key=bob.key

# 7. Create Context for Bob
kubectl config set-context bob-context   --cluster=$(kubectl config view --minify -o jsonpath='{.clusters[0].name}')   --namespace=dev-team   --user=developer-bob

# 8. Test Bob's permission (Will fail until role is bound)
kubectl --context=bob-context get pods
```

Now bind a role to group `developers`:

```bash
kubectl create rolebinding developers-pod-read   --role=pod-manager   --group=developers   --namespace=dev-team
```

Test again:

```bash
kubectl --context=bob-context get pods -n dev-team
# Success!
```

---

## 6. Complete Command Cheatsheet

| Task | Command |
| :--- | :--- |
| **Create Role** | `kubectl create role <name> --verb=<verbs> --resource=<resources> -n <ns>` |
| **Create ClusterRole** | `kubectl create clusterrole <name> --verb=<verbs> --resource=<resources>` |
| **Create RoleBinding** | `kubectl create rolebinding <name> --role=<role-name> --user=<user> -n <ns>` |
| **Bind to ServiceAccount** | `kubectl create rolebinding <name> --role=<role-name> --serviceaccount=<ns>:<sa-name> -n <ns>` |
| **Create ClusterRoleBinding** | `kubectl create clusterrolebinding <name> --clusterrole=<role> --user=<user>` |
| **Check Current User Auth** | `kubectl auth can-i <verb> <resource>` |
| **Check Auth as User** | `kubectl auth can-i <verb> <resource> --as=<username>` |
| **Check Auth as SA** | `kubectl auth can-i <verb> <resource> --as=system:serviceaccount:<ns>:<sa-name> -n <ns>` |
| **Reconcile RBAC YAML** | `kubectl auth reconcile -f rbac.yaml` |

---

## 7. Security Best Practices & Audit Checklist

1. **Avoid `*` (Wildcards):** Never grant wildcard verbs (`*`) or resources (`*`) in production unless creating a ClusterAdmin role.
2. **Minimize `cluster-admin` Use:** Reserve default `cluster-admin` for core infrastructure pipelines and Break-Glass procedures.
3. **Audit High-Risk Verbs:**
   * `escalate` / `bind`: Allows subjects to create permissions higher than what they currently possess.
   * `impersonate`: Allows a subject to act as any other user or ServiceAccount.
   * `pods/exec`: Allows remote code execution inside running containers.
   * `secrets`: Accessing secrets allows stealing database passwords and tokens.
4. **Disable Automatic SA Token Mounting:** If a pod does not need to talk to the K8s API server, set `automountServiceAccountToken: false` in the Pod/ServiceAccount spec.
5. **Use Audit Tools:** Regularly scan RBAC configurations using open-source tools:
   * **`kubesec` / `trivy`**: Static manifest security scanning.
   * **`rbac-tool`**: Visualize and simplify complex RBAC policies.
   * **`krane`**: RBAC static analysis and risk evaluation.

---

## 8. Troubleshooting Guide

### Common Error Messages & Solutions

#### 1. `Error from server (Forbidden): pods is forbidden: User "developer-bob" cannot list resource "pods"`

* **Cause:** User/SA lacks authorization for that namespace or verb.
* **Fix:** Check matching RoleBinding/ClusterRoleBinding using `kubectl get rolebindings,clusterrolebindings -A`. Verify subject name, namespace, and verb exact strings.

#### 2. `Service Account token not mounting`

* **Cause:** `automountServiceAccountToken` set to `false`, or API server failed token projection.
* **Fix:** Inspect SA spec: `kubectl get sa <sa-name> -o yaml`. Ensure field is `true` or omitted.

#### 3. `ClusterRole permissions not applying in namespace`

* **Cause:** Bound using `ClusterRoleBinding` when namespace isolation was expected, or bound with `RoleBinding` in the wrong target namespace.
* **Fix:** If namespace restriction is desired, bind `ClusterRole` with a `RoleBinding` inside the target namespace.
