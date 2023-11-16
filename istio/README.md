# Istio

## Install

1. Install helm repo

   ```bash
   helm repo add istio https://istio-release.storage.googleapis.com/charts
   helm repo update
   ```

1. Create namespace

   ```bash
   kubectl create namespace istio-system
   ```

1. Install istio chart

   ```bash
   helm install istio-base istio/base -n istio-system \
       --set defaultRevision=default
   ```

1. Verify istio installation

   ```bash
   helm ls -n istio-system
   ```

1. Install istio discovery chart

   ```bash
   helm install istiod istio/istiod -n istio-system --wait
   ```

1. Verify istio discovery installation

   ```bash
   helm ls -n istio-system
   ```

1. Check deployment

   ```bash
   kubectl get deployments -n istio-system -o wide
   ```

1. (Optional) Install ingress gateway

   ```bash
   kubectl create namespace istio-ingress
   helm install istio-ingress istio/gateway -n istio-ingress --wait
   ```

## Uninstall

1. List all istio charts installed

   ```bash
   helm ls -n istio-system
   ```

1. (Optional) Delete istio gateway chart

   ```bash
   helm delete istio-ingress -n istio-ingress
   kubectl delete namespace istio-ingress
   ```

1. Delete istio dicovery chart

   ```bash
   helm delete istiod -n istio-system
   ```

1. Delete istio chart

   ```bash
   helm delete istio-base -n istio-system
   ```

1. Delete istio namespace

   ```bash
   kubectl delete namespace istio-system
   ```

1. (Optional) Delete CRDs installed by istio

   ```bash
   kubectl get crd -oname | grep --color=never 'istio.io' | xargs kubectl delete
   ```
