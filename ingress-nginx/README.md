# Nginx Ingress Controller

1. Install requirements

```bash
# Install all requirements
kubectl apply -k ingress-nginx/

# Or install each components

# Namespace
kubectl apply -f ingress-nginx/ingress-nginx-namespace.yaml
```

1. Add repo

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
```

1. Install chart

```bash
helm install ingress-nginx ingress-nginx/ingress-nginx -n ingress-nginx
```

1. Verify nginx ingress is running

```bash
kubectl get deployment -n ingress-nginx
kubectl get service -n ingress-nginx
```
