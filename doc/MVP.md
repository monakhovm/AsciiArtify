# go-demo-app deploy on Kubernetes with argoCD

## 1. Create cluster role for self healing (`cluster-role.yaml`)

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole 
metadata: 
  name: argocd-self-heal
rules:
- apiGroups: ["*"]
  resources: ["*"] 
  verbs: ["*"]
```

## 2. Create application manifest for ArgoCD (`application.yaml`)

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application 
metadata: 
  name: demo
  namespace: argocd 
spec: 
  project: default
  source: 
    repoURL: https://github.com/den-vasyliev/go-demo-app.git
    path: helm
  destination:
    server: https://kubernetes.default.svc
    namespace: demo 
  syncPolicy: 
    automated:
      prune: true 
      selfHeal: true
    syncOptions:
    - CreateNamespace=true
    - ServerSideApply=true
    - ApplyOutOfSyncOnly=true
```

### 3. Apply the manifests

```bash
kubectl apply -f cluster-role.yaml -f application.yaml
```

![demo.gif](../assets/mvp/demo.gif)
