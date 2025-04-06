
## Create k3d cluster with LoadBalancer Service Support
```
k3d cluster create --api-port 6550 -p '80:80@loadbalancer' -p '443:443@loadbalancer' --agents 2 --k3s-arg '--disable=traefik@server:*'
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

# (Optional) Install Istio side-car pattern

### Install Istio Base components like CRDs and cluster roles
helm repo add istio https://istio-release.storage.googleapis.com/charts
helm install istio-base istio/base -n istio-system --create-namespace --wait

### Install Istiod Control plane node that will manage proxies
helm install istiod istio/istiod -n istio-system --wait

### (Skip this if you already have nginx ingress controller installed) Install Ingress gateway
helm install istio-ingressgateway istio/gateway -n istio-system --create-namespace --wait

### Label default namespace to include in Istio mesh
kubectl label namespace default istio-injection=enabled
