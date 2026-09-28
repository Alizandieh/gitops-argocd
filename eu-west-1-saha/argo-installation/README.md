# ArgoCD

We install the ArgoCD by helm chart

```
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update

# you need to modify this file first with the correct SSH private key
kubectl apply -f gitops-repo-credentials.yaml
kubectl apply -f helm-charts-repo-credentials.yaml

helm upgrade -i argocd argo/argo-cd -n argocd --create-namespace -f values.yaml

kubectl apply -f bootstrap-app.yaml
```
