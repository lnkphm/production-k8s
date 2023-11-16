# Production K8s

## Hardware Requirement

Minimum

| Type        | Number of Nodes | CPU     | RAM  |
| ----------- | --------------- | ------- | ---- |
| Master Node | 1               | 2 cores | 4 GB |

Single-node

| Type        | Number of Nodes | CPU     | RAM   |
| ----------- | --------------- | ------- | ----- |
| Master Node | 1               | 4 cores | 16 GB |

| Type        | Number of Nodes | CPU     | RAM   |
| ----------- | --------------- | ------- | ----- |
| Master Node | 1               | 8 cores | 32 GB |

Multi-node

| Type        | Number of Nodes | CPU     | RAM   |
| ----------- | --------------- | ------- | ----- |
| Master Node | 1               | 4 cores | 16 GB |
| Worker Node | 2               | 4 cores | 16 GB |

| Type        | Number of Nodes | CPU     | RAM   |
| ----------- | --------------- | ------- | ----- |
| Master Node | 3               | 4 cores | 16 GB |
| Worker Node | 3               | 4 cores | 16 GB |

## Providers

| Provider  | Cost                | Upgrade Cycle |
| --------- | ------------------- | ------------- |
| EKS       | $0.1/hr + Resource  | 3 months      |
| GKE       | $0.1/hr + Resource  | 3 months      |
| AKS       | Resource            | 3 months      |
| OpenShift | $0.17/hr + Resource | 3 months      |

## Deployment tools

- [kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/)
- [kOps](https://kubernetes.io/docs/setup/production-environment/tools/kops/)
- [Kubespray](https://kubernetes.io/docs/setup/production-environment/tools/kubespray/)

## Container Runtime

Prerequisites

- Ensure `br_netfilter` and `overlay` modules are loaded
- Ensure ip forward and bridge settings

```bash
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# sysctl params required by setup, params persist across reboots
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

# Apply sysctl params without reboot
sudo sysctl --system
```

Runtimes

- [containerd](https://github.com/containerd/containerd/blob/main/docs/getting-started.md)
- [cri-o](https://github.com/cri-o/cri-o/blob/main/install.md#readme)

## Container Storage

To be added...

## Pod Networking

- [Calico](https://www.tigera.io/project-calico/)
- [Cilium](https://github.com/cilium/cilium)

## Ingress Controller

- [Contour](https://projectcontour.io/)
- [Nginx Ingress](https://www.nginx.com/products/nginx-ingress-controller/)

## Service Mesh

- [Istio](https://istio.io/)

## Secret Management

- [Vault](https://developer.hashicorp.com/vault/docs/platform/k8s)

## Certificate Management

- [cert-manager](https://cert-manager.io/)

## Package Manager

- [Helm](https://helm.sh/)

## Chart Repository

- [Harbor](https://goharbor.io/)
- [ChartMuseum](https://github.com/helm/chartmuseum)
- [JFrog Artifactory](https://jfrog.com/artifactory/)

## CD

- [Argo CD](https://argo-cd.readthedocs.io/en/stable/)
- [Argo Workflow](https://argoproj.github.io/argo-workflows/)
- [Argo Rollouts](https://argoproj.github.io/argo-rollouts/)

## Observability

- [Prometheus](https://prometheus.io/)
- [Grafana](https://grafana.com/)
- [Loki](https://github.com/grafana/loki)
- [Jaeger](https://www.jaegertracing.io/)
- [Datadog](https://www.datadoghq.com/)
