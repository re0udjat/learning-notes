# Setup GitLab Runner with GitLab Runner Operator

**Step 1:** Install Cert Manager on K8s's cert-manager namespace:

[[Cert-manager#1. Install Cert-manager on K8s]]

**Step 2:** Install Operator Lifecycle Manager - OLM to help manage the Operators running on K8s's `olm` namespace:

[[Operator Lifecycle Manager - OLM#1. Install OLM on K8s]]

Then, install GitLab Runner Operator on K8s's `operators` namespace:

```shell
kubectl create -f https://operatorhub.io/install/gitlab-runner-operator.yaml
```

Verify that GitLab Runner Operator has been installed on K8s' `operators` namespace by checking Cluster Service Version:

```shell
kubectl get csv -n operators
```

**Step 3:** Create Runner Group on GitLab UI:

![[RegisterRunner.png]]

Create a K8s Secret to store authentication token obtained from the created Runner Group above:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: gitlab-runner-secret
  namespace: gitlab
stringData:
  runner-token: "glrt-ssXTuqJ7RQ3uxXyshHuJ"
```

Create a GitLab Runner from the following manifest:

```yaml
apiVersion: apps.gitlab.com/v1beta2
kind: Runner
metadata:
  name: gitlab-runner-vnshop
spec:
  gitlabUrl: https://git.poc.vnshop.cloud
  
  # Read Runner Group authentication token stored in K8s Secret
  token: gitlab-runner-secret 
```

# How the K8s Executor Work

K8s Executor calls the K8s cluster API and creates a Pod for each GitLab CI Job:

- **Step 1 - Prepare:** K8s create the Pod which includes Containers required for the build and services to run.
- **Step 2 - Pre-build:** K8s uses a special Container in Pod to clone, restore cache, and download artifacts from previous stages.
- **Step 3 - Build:** GitLab Runner do build.
- **Step 4 - Post-build:** K8s uses the special Container again to create cache, upload artifacts to GitLab.

![[K8sExecutorWorkflow.png]]

