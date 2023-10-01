# Setup kind to forward http & https requests to kind ingress controller using port mappings
```bash
kind create cluster --config=config.yaml
```
# Install ingress controller
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```
# Install cert-manager and create cert issuer
```bash
helm install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace --version v1.7.0 --set installCRDs=true --set image.repository=quay.io/jetstack/cert-manager-controller-arm64 --set prometheus.enabled=false 
kubectl apply -f issuer-prod.yaml
```
# Install ArgoCD
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
# Enable TLS HTTPS for ArgoCD UI
```bash
kubectl apply -f argo-ingress.yaml
```
