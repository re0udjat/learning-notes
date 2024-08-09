# 1. GitLab Runner + GitLab Runner Operator

> GitLab Runner Operator aims to manage the lifecycle of GitLab Runner instances in K8s or Openshift container platforms.

## 1.1. Setup GitLab Runner on K8s with GitLab Runner Operator

**Step 1:** [Install Cert Manager on K8s's `cert-manager` namespace](../Kubernetes/CRDs/Cert-manager.md#^3bee9b).

**Step 2:** [Install Operator Lifecycle Manager - OLM to help manage the Operators running on K8s's `olm` namespace](../Kubernetes/CRDs/Operator%20Lifecycle%20Manager%20-%20OLM.md#^f56b44).

Then, install GitLab Runner Operator on K8s's `operators` namespace:

```shell
kubectl create -f https://operatorhub.io/install/gitlab-runner-operator.yaml
```

Verify that GitLab Runner Operator has been installed on K8s' `operators` namespace by checking Cluster Service Version:

```shell
kubectl get csv -n operators
```

**Step 3:** Create Runner Group on GitLab UI:

![](./Images/GitLab/RegisterRunner.png)

Create a K8s Secret to store authentication token obtained from the created Runner Group above:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: gitlab-runner-secret
  namespace: gitlab-runner
stringData:
  runner-token: "REDACTED"
```

Create a GitLab Runner from the following manifest:

```yaml
apiVersion: apps.gitlab.com/v1beta2
kind: Runner
metadata:
  name: gitlab-runner-operator
  namespace: gitlab-runner
spec:
  gitlabUrl: https://gitlab.com
  
  # Read Runner Group authentication token stored in K8s Secret
  token: gitlab-runner-secret
  buildImage: "alpine" 
```
# 2. GitLab Runner + Helm

## 2.1. Setup GitLab Runner with Helm Chart

**Step 1:** Add the GitLab Helm repository:

```bash
helm repo add gitlab https://charts.gitlab.io && helm repo update gitlab
```

**Step 2:** Create Runner Group on GitLab UI:

![](./Images/GitLab/RegisterRunner.png)

Define a K8s Secret manifest `gitlab-runner-secret.yaml` to store authentication token obtained from the created Runner Group above:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: gitlab-runner-secret
  namespace: gitlab-runner
stringData:
  runner-registration-token: ""
  runner-token: "REDACTED"
```

Create Secret object on K8s:

```bash
kubectl apply -f gitlab-runner-secret.yaml
```

**Step 3:**  Create `values.yaml` file to customize GitLab Helm configuration:

```yaml
# The GitLab server full URL to register the runner against
gitlabUrl: https://gitlab.com

# Create RBAC rules for GitLab Runner to create Pods to run Jobs in
rbac:
  create: true

runners:
  # Authentication token which is stored directly in a secret instead of hard-coded string for security
  secret: gitlab-runner-secret
```

^937e08

Deploy GitLab Runner instance into K8s cluster:

```yaml
helm install --namespace gitlab-runner gitlab-runner -f values.yaml gitlab/gitlab-runner
```

## 2.2. Customize GitLab Runner Configuration

To change the behavior of GitLab Runner and individual registered runners, modify the [`values.yaml`](#^937e08) file:

```yaml
...
runners:
  secret: gitlab-runner-secret
  config: |
    concurrent = 15
    log_level = "debug"
    listen_address = "[::]:9252"

    [[runners]]
      url = "https://git.poc.vnshop.cloud"
      executor = "kubernetes"
      pull_policy = "if-not-present"
      [runner.kubernetes]
        image = "alpine"
```

Upgrade configuration changes:

```bash
helm upgrade --namespace gitlab-runner -f values.yaml gitlab-runner gitlab/gitlab-runner
```
## 2.3. How the K8s Executor Work

![](./Images/GitLab/K8sExecutorWorkflow.png)
