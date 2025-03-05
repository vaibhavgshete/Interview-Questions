## **Theoretical Questions & Answers**

---

### **1. What is Docker, and how does it differ from a virtual machine (VM)?**

- **Answer**:  
  Docker is a platform for developing, shipping, and running applications in **containers**. Containers are lightweight, isolated environments that package an application and its dependencies.
  
  - **Key Differences**:
    
    | **Docker Container**               | **Virtual Machine (VM)**                    |
    | ---------------------------------- | ------------------------------------------- |
    | Uses the host OS kernel.           | Requires a full OS (hypervisor + guest OS). |
    | Lightweight and fast to start.     | Heavier and slower to boot.                 |
    | Shares host resources efficiently. | Requires dedicated resources.               |
    | Less isolation (process-level).    | Strong isolation (hardware-level).          |

---

### **2. Explain the Docker architecture.**

- **Answer**:  
  Docker uses a **client-server architecture**:
  
  1. **Docker Daemon**: Background service (`dockerd`) managing containers, images, networks, etc.
  
  2. **Docker Client**: CLI (`docker`) used to interact with the daemon.
  
  3. **Docker Images**: Read-only templates for creating containers (e.g., `ubuntu:latest`).
  
  4. **Docker Containers**: Runnable instances of images.
  
  5. **Docker Registry**: Stores images (e.g., Docker Hub, AWS ECR).

---

### **3. Docker Image vs. Docker Container**

- **Answer**:
  
  - **Image**: A read-only template with application code, libraries, and configurations. Created using a `Dockerfile`.
  
  - **Container**: A running instance of an image. Containers are ephemeral (stateless) unless volumes are used.

---

### **4. Purpose of a Dockerfile**

- **Answer**:  
  A `Dockerfile` is a script with instructions to build a Docker image. Common instructions:
  
  dockerfile
  
  Copy
  
  FROM python:3.9-slim      # Base image
  WORKDIR /app             # Working directory in the container
  COPY . .                 # Copy files from host to container
  RUN pip install -r requirements.txt  # Execute commands during build
  CMD ["python", "app.py"] # Default command to run when the container starts

---

### **5. Docker Hub**

- **Answer**:  
  Docker Hub is a **public registry** for Docker images. It allows users to:
  
  - Pull public images (e.g., `docker pull nginx`).
  
  - Push custom images to repositories (e.g., `docker push myusername/myapp`).

---

### **6. Docker Volumes**

- **Answer**:  
  Volumes persist data beyond a container’s lifecycle. Use cases:
  
  - Databases (e.g., PostgreSQL data).
  
  - Configuration files.
  
  - **Example**:
    
    bash
    
    Copy
    
    docker volume create my_volume
    docker run -v my_volume:/data my_image

---

### **7. Docker Compose**

- **Answer**:  
  Docker Compose defines and runs multi-container apps using a YAML file (`docker-compose.yml`).
  
  - **Example**:
    
    yaml
    
    Copy
    
    version: '3'
    services:
      web:
    
        image: nginx
        ports:
          - "80:80"
    
      db:
    
        image: postgres
        environment:
          POSTGRES_PASSWORD: password

---

### **8. Docker Networking**

- **Answer**:  
  Docker provides default networks:
  
  - **Bridge**: Default network for containers (isolated).
  
  - **Host**: Shares the host’s network stack (no isolation).
  
  - **Overlay**: Connects multiple Docker daemons (Swarm mode).

---

### **9. `docker run` vs. `docker start`**

- **Answer**:
  
  - `docker run`: Creates and starts a **new container** from an image.
    
    bash
    
    Copy
    
    docker run -d --name my_container nginx
  
  - `docker start`: Restarts a **stopped container**.
    
    bash
    
    Copy
    
    docker start my_container

---

### **10. Namespaces and cgroups**

- **Answer**:
  
  - **Namespaces**: Isolate resources (PID, network, mount points).
  
  - **cgroups (Control Groups)**: Limit resource usage (CPU, memory, disk I/O).

---

### **11. `ENTRYPOINT` vs. `CMD`**

- **Answer**:
  
  - `ENTRYPOINT`: Defines the executable for the container (e.g., `["python"]`).
  
  - `CMD`: Provides default arguments to the `ENTRYPOINT` (e.g., `["app.py"]`).
  
  - If both are used: `ENTRYPOINT + CMD = python app.py`.

---

### **12. Optimizing Docker Images**

- **Answer**:
  
  - Use **multi-stage builds** to separate build and runtime environments.
  
  - Minimize layers (combine `RUN` commands).
  
  - Use small base images (e.g., `alpine`).
  
  - Remove unnecessary files (e.g., cache, logs).

---

### **13. Docker Swarm vs. Kubernetes**

- **Answer**:
  
  - **Docker Swarm**: Simple, native clustering for Docker. Best for small-scale deployments.
  
  - **Kubernetes**: More complex but offers advanced scaling, self-healing, and flexibility.

---

### **14. Docker Security Best Practices**

- **Answer**:
  
  - Avoid running containers as `root`.
  
  - Use minimal base images (e.g., `scratch`).
  
  - Scan images for vulnerabilities (e.g., `docker scan`).
  
  - Limit resource usage with `cgroups`.

---

### **15. `docker pull` and `docker push`**

- **Answer**:
  
  - `docker pull`: Downloads an image from a registry.
    
    bash
    
    Copy
    
    docker pull nginx:latest
  
  - `docker push`: Uploads an image to a registry.
    
    bash
    
    Copy
    
    docker push myrepo/myimage:tag

---

## **Practical Questions & Answers**

---

### **1. Write a Dockerfile for a Python App**

- **Answer**:
  
  dockerfile
  
  Copy
  
  # Use a lightweight Python image
  
  FROM python:3.9-slim
  
  # Set working directory
  
  WORKDIR /app
  
  # Copy dependencies and install
  
  COPY requirements.txt .
  RUN pip install --no-cache-dir -r requirements.txt
  
  # Copy application code
  
  COPY . .
  
  # Run the application
  
  CMD ["python", "app.py"]

---

### **2. Run a Container in Detached Mode and View Logs**

- **Commands**:
  
  bash
  
  Copy
  
  # Start container in detached mode
  
  docker run -d --name my_app nginx
  
  # View logs
  
  docker logs my_app
  
  # Follow logs in real-time
  
  docker logs -f my_app

---

### **3. Clean Up Stopped Containers and Unused Images**

- **Commands**:
  
  bash
  
  Copy
  
  # Remove all stopped containers
  
  docker container prune
  
  # Remove unused images
  
  docker image prune -a
  
  # Remove everything (containers, networks, images)
  
  docker system prune -a

---

### **4. Expose a Container Port to the Host**

- **Command**:
  
  bash
  
  Copy
  
  # Map host port 8080 to container port 80
  
  docker run -p 8080:80 nginx

---

### **5. Create and Use a Docker Volume**

- **Commands**:
  
  bash
  
  Copy
  
  # Create a volume
  
  docker volume create mydata
  
  # Mount the volume to a container
  
  docker run -v mydata:/data my_image

---

### **6. Docker Compose for Web App and Database**

- **Example**:
  
  yaml
  
  Copy
  
  version: '3'
  services:
    web:
  
      image: my_web_app:latest
      ports:
        - "5000:5000"
      depends_on:
        - db
  
    db:
  
      image: postgres:13
      environment:
        POSTGRES_PASSWORD: mypassword
      volumes:
        - postgres_data:/var/lib/postgresql/data
  
  volumes:
    postgres_data:

---

### **7. Limit CPU and Memory Usage**

- **Command**:
  
  bash
  
  Copy
  
  docker run --cpus="1.5" --memory="512m" my_image

---

### **8. Debug a Crashing Container**

- **Steps**:
  
  1. Check logs:
     
     bash
     
     Copy
     
     docker logs my_container
  
  2. Run interactively to inspect:
     
     bash
     
     Copy
     
     docker run -it --entrypoint /bin/sh my_image

---

### **9. Tag and Push an Image to Docker Hub**

- **Commands**:
  
  bash
  
  Copy
  
  # Tag the image
  
  docker tag my_image:latest username/my_image:v1
  
  # Push to Docker Hub
  
  docker push username/my_image:v1

---

### **10. Inspect a Running Container**

- **Command**:
  
  bash
  
  Copy
  
  docker inspect my_container
  
  # View specific details (e.g., IP address)
  
  docker inspect -f '{{ .NetworkSettings.IPAddress }}' my_container

---

### **11. Multi-Stage Build Example**

- **Dockerfile**:
  
  dockerfile
  
  Copy
  
  # Build stage
  
  FROM node:14 AS builder
  WORKDIR /app
  COPY . .
  RUN npm install && npm run build
  
  # Runtime stage
  
  FROM nginx:alpine
  COPY --from=builder /app/dist /usr/share/nginx/html

---

### **12. Connect to a Container’s Shell**

- **Command**:
  
  bash
  
  Copy
  
  docker exec -it my_container /bin/bash

---

### **13. Set Environment Variables**

- **Command**:
  
  bash
  
  Copy
  
  docker run -e MY_ENV_VAR=value my_image
  
  # In Docker Compose:
  
  environment:
    MY_ENV_VAR: value

---

### **14. Clean Up Docker Resources**

- **Command**:
  
  bash
  
  Copy
  
  # Remove all unused networks, images, containers
  
  docker system prune -a

---

### **15. Scale Services with Docker Compose**

- **Command**:
  
  bash
  
  Copy
  
  docker-compose up --scale web=5  # Run 5 instances of the "web" service

---

## **Scenario-Based Questions & Answers**

---

### **1. Deploy a Microservices App with Docker**

- **Answer**:  
  Use **Docker Compose** for local development:
  
  yaml
  
  Copy
  
  services:
    auth_service:
  
      image: auth:latest
      ports:
        - "3000:3000"
  
    order_service:
  
      image: orders:latest
      ports:
        - "3001:3000"
  
  For production, use **Kubernetes** with deployments, services, and ingress.

---

### **2. Troubleshoot a Container Running Out of Memory**

- **Steps**:
  
  1. Check memory usage:
     
     bash
     
     Copy
     
     docker stats my_container
  
  2. Adjust memory limits:
     
     bash
     
     Copy
     
     docker run --memory="1g" my_image
  
  3. Profile the app (e.g., check for memory leaks).

---

### **3. Ensure High Availability in Production**

- **Answer**:  
  Use **Docker Swarm** or **Kubernetes** to:
  
  - Distribute containers across nodes.
  
  - Auto-restart failed containers.
  
  - Load balance traffic.

---

### **4. Secure Docker in CI/CD**

- **Best Practices**:
  
  - Scan images with tools like **Trivy** or **Clair**.
  
  - Use **signed images** from trusted registries.
  
  - Implement **RBAC** (Role-Based Access Control).

---

### **5. Migrate a Legacy App to Docker**

- **Steps**:
  
  1. Containerize the app using a `Dockerfile`.
  
  2. Test locally with Docker Compose.
  
  3. Deploy to production using Kubernetes.

---
