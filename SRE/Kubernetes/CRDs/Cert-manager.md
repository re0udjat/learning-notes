> Cert-manager creates TLS certificates for workloads in K8s or OpenShift cluster and renews the certificates before they expire.

# 1. Install Cert-manager on K8s

^3bee9b

To install cert-manager on K8s, run:

```shell
kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.7.1/cert-manager.yaml
```