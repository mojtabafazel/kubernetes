1. Install Argo CD
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml


If you are not interested in UI, SSO, multi-cluster features then you can install core Argo CD components only:

kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/core-install.yaml


for find a argocd password

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
or
kubectl -n argocd get secrets argocd-initial-admin-secret -o yaml
---
apiVersion: v1
data:
  password: ZWx2TGlTREhiZDVXeGpXMQ==
kind: Secret
metadata:
  creationTimestamp: "2023-06-27T18:17:07Z"
  name: argocd-initial-admin-secret
  namespace: argocd
  resourceVersion: "183576"
  uid: 0ab872c2-ebb6-4eaf-9877-0b4b720af812
type: Opaque
---
echo "ZWx2TGlTREhiZDVXeGpXMQ" | base64 -d

