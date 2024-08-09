> Kustomize is a standalone tool to customize K8s configurations through a kustomization file.

^3db826

# Generate ConfigMap From File

The old way to create ConfigMap from file:

```bash
kubectl create cm <CONFIGMAP_NAME> --from-file <CONFIGMAP_KEY>=<CONFIG_FILE>
```

The new way to create ConfigMap from kustomization file with kustomize:

```yaml
apiVersion: kusomize.config.k8s.io/v1beta1
kind: Kustomization
configMapGenerator:
- name: <CONFIGMAP_NAME>
  files:
  - <CONFIG_FILE>
```