---
# GitLab Runner Autoscaling Setup on Kubernetes

## **Design Overview**
- **Goal**: Automatically scale GitLab Runner pods based on job demand (up to 12 concurrent jobs).
- **Infrastructure**:
  - Server: 128vCPU, 100GB RAM.
  - Per Job: 10vCPU, 8GB RAM.
  - Max Concurrent Jobs: `12` (min(128/10, 100/8)).
- **Components**:
  - Kubernetes Cluster (single-node using `k3s`).
  - GitLab Runner (managed via Helm).
  - Always-Available "Warm Pod" (1 replica).
- **Tools**:
  - `k3s` (Kubernetes distribution).
  - Helm (package manager).
  - GitLab Runner Helm Chart.

---

## **Step-by-Step Setup**

### **1. Install Kubernetes (k3s)**

```bash
Install k3s (disable Traefik and local storage)

curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--disable traefik --disable local-storage" sh -

# Verify node status

sudo kubectl get nodes
```

### **2. Install Helm and kubectl**

```markdown
Install kubectl

sudo apt-get update && sudo apt-get install -y kubectl

# Install Helm

curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

# Configure k3s access

mkdir -p ~/.kube
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
sudo chown $(id -u):$(id -g) ~/.kube/config
```



---

### **3. Create Namespace**



kubectl create namespace gitlab-runner

---

### **4. Deploy GitLab Runner via Helm**

#### **Helm Configuration (`gitlab-runner-values.yaml`)**

yaml

Copy

# gitlab-runner-values.yaml

gitlabUrl: "https://gitlab.com"
runnerRegistrationToken: "YOUR_RUNNER_TOKEN"  # Replace with your token
concurrent: 12
checkInterval: 30

runners:
  config: |
    [[runners]]
      [runners.kubernetes]
        namespace = "gitlab-runner"
        cpu_request = "10000m"    # 10vCPU
        memory_request = "8192Mi" # 8GB RAM
        cpu_limit = "10000m"
        memory_limit = "8192Mi"
        [runners.kubernetes.node_selector]
          workload = "ci"
        [runners.kubernetes.tolerations]
          [[runners.kubernetes.tolerations]]
            key = "workload"
            operator = "Equal"
            value = "ci"
            effect = "NoSchedule"

resources:
  limits:
    cpu: "1000m"
    memory: "512Mi"

#### **Install GitLab Runner**

bash

Copy

# Add GitLab Helm repo

helm repo add gitlab https://charts.gitlab.io
helm repo update

# Deploy the runner

helm install gitlab-runner gitlab/gitlab-runner \
  -f gitlab-runner-values.yaml \
  --namespace gitlab-runner

---

### **5. Deploy Always-Available Warm Pod**

#### **Manifest File (`warm-pod.yaml`)**

yaml

Copy

# warm-pod.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: gitlab-runner-warm-pod
  namespace: gitlab-runner
spec:
  replicas: 1
  selector:
    matchLabels:
      app: gitlab-runner-warm
  template:
    metadata:
      labels:
        app: gitlab-runner-warm
    spec:
      containers:
      - name: gitlab-runner
        image: gitlab/gitlab-runner:latest
        env:
          - name: CI_SERVER_URL
            value: "https://gitlab.com"
          - name: REGISTRATION_TOKEN
            value: "YOUR_RUNNER_TOKEN"  # Same token as above
          - name: RUNNER_EXECUTOR
            value: "kubernetes"
        command: ["gitlab-runner", "run"]
      nodeSelector:
        workload: "ci"
      tolerations:
        - key: "workload"
          operator: "Equal"
          value: "ci"
          effect: "NoSchedule"

#### **Apply the Manifest**

bash

Copy

kubectl apply -f warm-pod.yaml

---

### **6. Taint and Label the Node**

bash

Copy

# Replace <node-name> with your node's name (use `kubectl get nodes` to find it)

kubectl taint nodes <node-name> workload=ci:NoSchedule
kubectl label nodes <node-name> workload=ci

---

### **7. Verify Setup**

bash

Copy

# Check running pods

kubectl get pods -n gitlab-runner

# Expected output:

# gitlab-runner-...          (Manager pod)

# gitlab-runner-warm-pod-... (Warm pod)

---

## **Autoscaling Workflow**

1. **Job Submission**:
   
   - The warm pod picks up the first job immediately.

2. **Autoscale Trigger**:
   
   - Subsequent jobs trigger new pod creation (up to 12).

3. **Resource Limits**:
   
   - Kubernetes enforces 10vCPU/8GB RAM per job.

4. **Cleanup**:
   
   - Completed pods are automatically deleted.

---

## **Monitoring (Optional)**

### **Install Prometheus & Grafana**

bash

Copy

# Add Prometheus Helm repo

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm install prometheus prometheus-community/kube-prometheus-stack -n monitoring

### **Key Metrics to Track**

- Node CPU/RAM usage.

- Pending jobs/pods.

- Pod startup time.

---

## **Troubleshooting**

| **Issue**               | **Solution**                                                      |
| ----------------------- | ----------------------------------------------------------------- |
| Jobs stuck in `pending` | Check node resources: `kubectl describe node`                     |
| Pod creation failures   | Inspect runner logs: `kubectl logs -n gitlab-runner <runner-pod>` |
| Token/permission errors | Verify `runnerRegistrationToken` in Helm values.                  |

---

## **References**

1. [GitLab Runner Helm Chart](https://docs.gitlab.com/runner/install/kubernetes.html)

2. [k3s Installation Guide](https://docs.k3s.io/quick-start)

3. [Kubernetes Taints/Tolerations](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/)

---

## **Summary**

- **Autoscaling**: Up to 12 jobs with dynamic pod creation.

- **Warm Pod**: 1 always-ready runner.

- **Resource Safety**: Kubernetes enforces per-job limits.

Save this guide and adapt values (e.g., tokens, node names) for your environment. Let me know if you need clarifications! 🚀
