### **Theoretical Questions with Detailed Answers**

#### **Basic Concepts**

1. **What is Kubernetes, and why is it used?**
   
   - Kubernetes
     is an open-source container orchestration platform designed to automate
     the deployment, scaling, and management of containerized applications.
   
   - It is used to:
     
     - Manage containerized applications across multiple hosts.
     
     - Automate scaling and load balancing.
     
     - Ensure high availability and fault tolerance.
     
     - Simplify rolling updates and rollbacks.
     
     - Provide a unified API for managing infrastructure.

2. **Explain the architecture of Kubernetes (Master and Worker nodes).**
   
   - **Master Node**:
     
     - **API Server**: Exposes the Kubernetes API.
     
     - **Controller Manager**: Manages controllers like Node Controller, Replication Controller, etc.
     
     - **Scheduler**: Assigns Pods to Nodes.
     
     - **etcd**: A distributed key-value store for cluster data.
   
   - **Worker Node**:
     
     - **Kubelet**: Ensures containers are running in Pods.
     
     - **Kube Proxy**: Manages network rules and load balancing.
     
     - **Container Runtime**: Runs containers (e.g., Docker, containerd).

3. **What are the main components of the Kubernetes control plane?**
   
   - **API Server**: Front-end for the Kubernetes control plane.
   
   - **etcd**: Stores cluster state and configuration.
   
   - **Controller Manager**: Manages core control loops.
   
   - **Scheduler**: Assigns workloads to Nodes.

4. **What is a Pod in Kubernetes, and why is it the smallest deployable unit?**
   
   - A Pod is the smallest and simplest unit in Kubernetes. It represents a single instance of a running process in a cluster.
   
   - It can contain one or more containers that share:
     
     - The same network namespace (IP address).
     
     - Storage volumes.
     
     - Resources like memory and CPU.
   
   - Pods are ephemeral and can be created, destroyed, or replaced dynamically.

5. **What is the difference between a Deployment and a StatefulSet?**
   
   - **Deployment**:
     
     - Used for stateless applications.
     
     - Pods are interchangeable and can be replaced without affecting the application.
     
     - Supports rolling updates and rollbacks.
   
   - **StatefulSet**:
     
     - Used for stateful applications (e.g., databases).
     
     - Pods have unique, stable network identities and persistent storage.
     
     - Pods are created and deleted in a specific order.

6. **What is a Namespace in Kubernetes, and how is it useful?**
   
   - A Namespace is a virtual cluster within a physical cluster. It is used to:
     
     - Organize resources (e.g., dev, staging, prod).
     
     - Isolate resources between teams or projects.
     
     - Apply resource quotas and limits.
   
   - Example: `kubectl create namespace dev`.

7. **What is the difference between a Service and an Ingress in Kubernetes?**
   
   - **Service**:
     
     - Provides stable networking for Pods (e.g., ClusterIP, NodePort, LoadBalancer).
     
     - Routes traffic to Pods using labels and selectors.
   
   - **Ingress**:
     
     - Manages external access to Services (e.g., HTTP/HTTPS).
     
     - Provides features like SSL termination and path-based routing.
     
     - Requires an Ingress Controller (e.g., Nginx, Traefik).

8. **What is a ConfigMap, and how is it different from a Secret?**
   
   - **ConfigMap**:
     
     - Stores non-sensitive configuration data (e.g., environment variables, configuration files).
     
     - Example: `kubectl create configmap my-config --from-literal=key=value`.
   
   - **Secret**:
     
     - Stores sensitive data (e.g., passwords, API keys).
     
     - Data is base64-encoded by default.
     
     - Example: `kubectl create secret generic my-secret --from-literal=password=secret`.

9. **What is the role of the Kubelet in a Kubernetes cluster?**
   
   - The Kubelet is an agent that runs on each worker node. It:
     
     - Ensures containers are running in Pods.
     
     - Communicates with the API server to receive instructions.
     
     - Reports node and Pod status to the control plane.

10. **What is the difference between `kubectl apply` and `kubectl create`?**
    
    - `kubectl create`: Creates a new resource (fails if the resource already exists).
    
    - `kubectl apply`: Creates or updates a resource based on the YAML file (idempotent).

---

### **Practical Questions with Detailed Answers**

#### **Basic Tasks**

1. **Create a simple Pod using a YAML file.**
   
   yaml
   
   Copy
   
   apiVersion: v1
   kind: Pod
   metadata:
    name: my-pod
   spec:
    containers:
   
   - name: nginx
     image: nginx:latest
   
   - Save the file as `pod.yaml` and run:
     
     bash
     
     Copy
     
     kubectl apply -f pod.yaml

2. **Create a Deployment that runs 3 replicas of an Nginx container.**
   
   yaml
   
   Copy
   
   apiVersion: apps/v1
   kind: Deployment
   metadata:
    name: nginx-deployment
   spec:
    replicas: 3
    selector:
      matchLabels:
   
        app: nginx
   
    template:
      metadata:
   
        labels:
          app: nginx
   
      spec:
   
        containers:
        - name: nginx
          image: nginx:latest
   
   - Save the file as `deployment.yaml` and run:
     
     bash
     
     Copy
     
     kubectl apply -f deployment.yaml

3. **Expose a Deployment using a Service of type NodePort.**
   
   yaml
   
   Copy
   
   apiVersion: v1
   kind: Service
   metadata:
    name: nginx-service
   spec:
    type: NodePort
    selector:
      app: nginx
    ports:
   
   - port: 80
     targetPort: 80
   
   - Save the file as `service.yaml` and run:
     
     bash
     
     Copy
     
     kubectl apply -f service.yaml

4. **Create a ConfigMap and use it to pass configuration data to a Pod.**
   
   yaml
   
   Copy
   
   apiVersion: v1
   kind: ConfigMap
   metadata:
    name: my-config
   data:
    key: value
   
   - Use it in a Pod:
     
     yaml
     
     Copy
     
     env:
     
     - name: KEY
       valueFrom:
        configMapKeyRef:
          name: my-config
          key: key

5. **Create a Secret and use it to pass sensitive data to a Pod.**
   
   bash
   
   Copy
   
   kubectl create secret generic my-secret --from-literal=password=secret
   
   - Use it in a Pod:
     
     yaml
     
     Copy
     
     env:
     
     - name: PASSWORD
       valueFrom:
        secretKeyRef:
          name: my-secret
          key: password

---

### **Scenario-Based Questions with Detailed Answers**

1. **How would you design a Kubernetes cluster for a high-availability application?**
   
   - Use multiple master nodes with `etcd` in a clustered configuration.
   
   - Distribute worker nodes across availability zones.
   
   - Use LoadBalancer Services for external access.
   
   - Implement PodDisruptionBudgets and Horizontal Pod Autoscalers.

2. **How would you handle a situation where a Pod is not starting due to resource constraints?**
   
   - Check resource requests and limits in the Pod definition.
   
   - Use `kubectl describe pod <pod-name>` to identify the issue.
   
   - Increase node resources or scale the cluster.

3. **How would you debug a Service that is not routing traffic to the correct Pods?**
   
   - Verify the Service's selector matches the Pod labels.
   
   - Check Pod readiness and liveness probes.
   
   - Use `kubectl get endpoints` to ensure the Service has endpoints.

4. **How would you secure sensitive data (e.g., database credentials) in Kubernetes?**
   
   - Use Secrets to store sensitive data.
   
   - Restrict access using RBAC.
   
   - Encrypt Secrets at rest using `etcd` encryption.

5. **How would you monitor and log applications running in a Kubernetes cluster?**
   
   - Use tools like Prometheus for monitoring and Grafana for visualization.
   
   - Use Fluentd or Elasticsearch for logging.
   
   - Implement centralized logging with a tool like Loki.

---
