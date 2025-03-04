## **Practical AWS Questions**

1. **You need to create a highly available architecture using AWS services. How would you design it?**
   
   - Explain using services like **ELB, Auto Scaling, Multi-AZ RDS, and Route 53**.

2. **You need to automate deployments using AWS CodePipeline, but the build is failing in CodeBuild. How would you troubleshoot?**
   
   - Check logs in **AWS CodeBuild**.
   - Validate `buildspec.yml` syntax.
   - Check IAM permissions.
   - Verify environment variables.

3. **Your AWS Lambda function is running slow. How would you debug and optimize it?**
   
   - Use **AWS X-Ray for tracing**.
   - Optimize function **memory allocation**.
   - Reduce **cold starts** using provisioned concurrency.
   - Optimize database queries if applicable.

4. **How would you securely store secrets and credentials in AWS?**
   
   - Use **AWS Secrets Manager** or **Parameter Store**.
   - Apply IAM policies with least privilege.

5. **A CI/CD pipeline deploys an application to EC2, but the deployment fails. How do you troubleshoot?**
   
   - Check AWS **CodeDeploy logs**.
   - Ensure the **IAM role** has necessary permissions.
   - Verify the EC2 instance is in the correct **Auto Scaling Group**.

---

## **Practical Docker Questions**

6. **Your Docker container is running out of memory. How would you investigate and fix it?**
   
   - Use `docker stats` to monitor memory usage.
   - Limit memory with `--memory` flag.
   - Optimize the application to use less memory.

7. **You need to reduce the size of your Docker image. How would you achieve that?**
   
   - Use a **smaller base image** (e.g., `alpine` instead of `ubuntu`).
   - **Multi-stage builds** to remove unnecessary files.
   - Use `.dockerignore` to exclude unnecessary files.

8. **A containerized application is unable to connect to a database running on another container. How do you fix it?**
   
   - Ensure both containers are in the same **Docker network**.
   - Use `docker network inspect` to check connectivity.
   - Check **environment variables** for the correct database host.

9. **How do you persist data across container restarts?**
   
   - Use **Docker Volumes**: `docker volume create mydata`.
   - Mount volumes in containers: `docker run -v mydata:/data`.

10. **How would you monitor running containers?**
    
    - Use `docker stats` for real-time resource usage.
    - Enable **Docker logging drivers**.
    - Use tools like **Prometheus + Grafana** for long-term monitoring.

---

## **Practical Kubernetes Questions**

11. **How would you deploy a scalable web application on Kubernetes?**
    
    - Create **Deployment with ReplicaSet**.
    - Expose it via a **Kubernetes Service (LoadBalancer or Ingress)**.
    - Use **Horizontal Pod Autoscaler (HPA)** for scaling.

12. **Your Kubernetes Pod keeps restarting. How would you troubleshoot?**
    
    - Run `kubectl describe pod <pod-name>` to check **Events**.
    - Check logs with `kubectl logs <pod-name>`.
    - Investigate **readiness/liveness probes**.

13. **A Kubernetes service is not accessible from outside the cluster. How do you debug?**
    
    - Verify `kubectl get svc` to check **service type**.
    - Ensure **Ingress Controller** is configured correctly.
    - Use `kubectl port-forward` for local testing.

14. **How do you handle secret management in Kubernetes?**
    
    - Use **Kubernetes Secrets**: `kubectl create secret generic my-secret --from-literal=password=supersecret`.
    - Mount secrets as environment variables or files.

15. **How would you monitor a Kubernetes cluster?**
    
    - Use **Prometheus + Grafana** for metrics.
    - Enable **Kubernetes Logging (Fluentd, ELK Stack)**.
    - Use **kubectl top pods** to check resource usage.
