# Cilium

## Install Cilium

### Cilium CLI

1. Create K8s cluster

   ```bash
   # minikube
   minikube start --network-plugin=cni --cni=false
   ```

1. Install Cilium CLI

   ```bash
   CILIUM_CLI_VERSION=$(curl -s https://raw.githubusercontent.com/cilium/cilium-cli/main/stable.txt)
   CLI_ARCH=amd64
   if [ "$(uname -m)" = "aarch64" ]; then CLI_ARCH=arm64; fi
   curl -L --fail --remote-name-all https://github.com/cilium/cilium-cli/releases/download/${CILIUM_CLI_VERSION}/cilium-linux-${CLI_ARCH}.tar.gz{,.sha256sum}
   sha256sum --check cilium-linux-${CLI_ARCH}.tar.gz.sha256sum
   sudo tar xzvfC cilium-linux-${CLI_ARCH}.tar.gz /usr/local/bin
   rm cilium-linux-${CLI_ARCH}.tar.gz{,.sha256sum}
   ```

1. Install Cilium

   ```bash
   cilium install --version 1.14.4
   ```

1. Validate installation

   ```bash
   # Validate cilium installation
   cilium status --wait

   # Validate cluster network connectivity
   cilium connectivity test
   ```

### Helm

1. Add chart repo

   ```bash
   helm repo add cilium https://helm.cilium.io/
   helm repo update
   ```

1. Deploy Cilium

   ```bash
   helm install cilium cilium/cilium --version 1.14.4 --namespace kube-system
   ```

1. Restart unmanaged pods

   ```bash
   kubectl get pods --all-namespaces -o custom-columns=NAMESPACE:.metadata.namespace,NAME:.metadata.name,HOSTNETWORK:.spec.hostNetwork --no-headers=true | grep '<none>' | awk '{print "-n "$1" "$2}' | xargs -L 1 -r kubectl delete pod
   ```

1. Validate installation

   ```bash
   # Install cilium-cli
   CILIUM_CLI_VERSION=$(curl -s https://raw.githubusercontent.com/cilium/cilium-cli/main/stable.txt)
   CLI_ARCH=amd64
   if [ "$(uname -m)" = "aarch64" ]; then CLI_ARCH=arm64; fi
   curl -L --fail --remote-name-all https://github.com/cilium/cilium-cli/releases/download/${CILIUM_CLI_VERSION}/cilium-linux-${CLI_ARCH}.tar.gz{,.sha256sum}
   sha256sum --check cilium-linux-${CLI_ARCH}.tar.gz.sha256sum
   sudo tar xzvfC cilium-linux-${CLI_ARCH}.tar.gz /usr/local/bin
   rm cilium-linux-${CLI_ARCH}.tar.gz{,.sha256sum}

   # Validate cilium
   cilium status --wait

   # Validate cluster network connectivity
   cilium connectivity test
   ```

## Setting up Hubble

1. Enable Hubble in Cilium

   Cilium CLI

   ```bash
   cilium hubble enable
   ```

   Helm

   ```bash
   helm upgrade cilium cilium/cilium --version 1.14.4 \
       --namespace kube-system \
       --reuse-values \
       --set hubble.relay.enabled=true \
       --set hubble.ui.enabled=true
   ```

1. Install Hubble Client

   ```bash
   HUBBLE_VERSION=$(curl -s https://raw.githubusercontent.com/cilium/hubble/master/stable.txt)
   HUBBLE_ARCH=amd64
   if [ "$(uname -m)" = "aarch64" ]; then HUBBLE_ARCH=arm64; fi
   curl -L --fail --remote-name-all https://github.com/cilium/hubble/releases/download/$HUBBLE_VERSION/hubble-linux-${HUBBLE_ARCH}.tar.gz{,.sha256sum}
   sha256sum --check hubble-linux-${HUBBLE_ARCH}.tar.gz.sha256sum
   sudo tar xzvfC hubble-linux-${HUBBLE_ARCH}.tar.gz /usr/local/bin
   rm hubble-linux-${HUBBLE_ARCH}.tar.gz{,.sha256sum}
   ```

1. Validate Hubble API Access

   ```bash
   # Forward hubble port
   cilium hubble port-forward&

   # Check access to Hubble API
   hubble status

   # Check API flow
   hubble observe
   ```

## Enable Metrics

1. Enable metrics with helm

   ```bash
   helm upgrade cilium cilium/cilium --version 1.14.4 \
       --namespace kube-system \
       --reuse-values \
       --set prometheus.enabled=true \
       --set operator.prometheus.enabled=true \
       --set hubble.enabled=true \
       --set hubble.metrics.enableOpenMetrics=true \
       --set hubble.metrics.enabled="{dns,drop,tcp,flow,port-distribution,icmp,httpV2:exemplars=true;labelsContext=source_ip\,source_namespace\,source_workload\,destination_ip\,destination_namespace\,destination_workload\,traffic_direction}"
   ```

1. Prometheus & Grafana Deployment

   ```bash
   kubectl apply -f https://raw.githubusercontent.com/cilium/cilium/1.14.4/examples/kubernetes/addons/prometheus/monitoring-example.yaml
   ```
