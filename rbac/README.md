# 🔐 Kubernetes Service Account & RBAC Manifests

This directory contains clean, modular Kubernetes RBAC (Role-Based Access Control) manifests for managing application identities and permissions.

---

## 📁 File Structure

| File | Resource Kind | Description |
| :--- | :--- | :--- |
| [service-account.yaml](file:///home/nan/me-workspace/infra-k8s/k8s-manifests/rbac/service-account.yaml) | `ServiceAccount` | Defines the identity (`app-service-account`) for pods in `dnan-prod`. |
| [role.yaml](file:///home/nan/me-workspace/infra-k8s/k8s-manifests/rbac/role.yaml) | `Role` | Grants read-only access (`get`, `list`, `watch`) to Secrets & ConfigMaps. |
| [role-binding.yaml](file:///home/nan/me-workspace/infra-k8s/k8s-manifests/rbac/role-binding.yaml) | `RoleBinding` | Binds `app-service-account` to `app-secret-reader-role`. |

---

## 🚀 How to Apply

Apply all RBAC manifests to your cluster:

```bash
kubectl apply -f ~/me-workspace/infra-k8s/k8s-manifests/rbac/
```

---

## 🧪 How to Test RBAC Permissions

Use `kubectl auth can-i` to verify what your ServiceAccount can and cannot do:

### 1. Test if ServiceAccount can list secrets (Should return `yes`):
```bash
kubectl auth can-i list secrets \
  --as=system:serviceaccount:dnan-prod:app-service-account \
  -n dnan-prod
```

### 2. Test if ServiceAccount can delete secrets (Should return `no`):
```bash
kubectl auth can-i delete secrets \
  --as=system:serviceaccount:dnan-prod:app-service-account \
  -n dnan-prod
```

---

## 📦 How to Attach to a Pod / Deployment

Add `serviceAccountName: app-service-account` inside your Deployment's `spec.template.spec`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  namespace: dnan-prod
spec:
  replicas: 1
  template:
    spec:
      serviceAccountName: app-service-account # <--- Attaches the ServiceAccount Identity!
      containers:
        - name: app
          image: nginx:alpine
```
