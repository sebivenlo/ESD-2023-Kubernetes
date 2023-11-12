# Comprehensive Kubernetes Workshop Guide

## Setting Up Minikube and Using kubectl

### Windows Set Up:

1. **Install Chocolatey**: A package manager that makes it easier to manage software installations on Windows.
   - Run this command to install Chocolatey from an administrator command prompt. This simplifies future installations and updates.
     ```
     Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
     ```

2. **Install Docker** using Chocolatey:
   - Docker is essential for creating and managing containers. This command installs Docker, which Minikube will use to run Kubernetes clusters.
     ```
     choco install docker-desktop
     ```

3. **Install Minikube** using Chocolatey:
   - Minikube is a tool that runs a single-node Kubernetes cluster locally. This installation is crucial for setting up a local Kubernetes environment.
     ```
     choco install minikube
     ```

4. **Install kubectl** using Chocolatey:
   - `kubectl` is the Kubernetes command-line tool, allowing you to run commands against your clusters. This step installs `kubectl`, which is used for managing and deploying applications on Kubernetes.
     ```
     choco install kubernetes-cli
     ```

5. **Start Minikube with Docker Driver**:
   - This command starts up Minikube using Docker as the virtualization driver. It allocates resources (like memory) to the Minikube VM.
     ```
     minikube start --driver=docker --memory=2048
     ```

### MacOS Set Up:

1. **Install Homebrew**:
   - Homebrew is a package manager for MacOS. This command installs Homebrew, which is used for managing software installations on MacOS.
     ```
     /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
     ```

2. **Install Docker** using Homebrew:
   - Similar to the Windows setup, this installs Docker on MacOS for container management.
     ```
     brew install --cask docker
     ```

3. **Install Minikube and kubectl** using Homebrew:
   - This command installs both Minikube and kubectl on MacOS, setting up your Kubernetes environment.
     ```
     brew install minikube kubernetes-cli
     ```

4. **Start Minikube**:
   - Starts the Minikube Kubernetes cluster using the Docker driver on MacOS.
     ```
     minikube start --driver=docker
     ```

### Linux Set Up:

1. **Install Docker** using the package manager:
   - Installs Docker on Linux distributions, a prerequisite for running Minikube and Kubernetes.
     ```
     sudo apt-get update
     sudo apt-get install docker.io
     ```

2. **Install Minikube and kubectl**:
   - This set of commands downloads and installs Minikube and kubectl directly from their official sources, setting up the Kubernetes environment on Linux.
     ```
     curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
     sudo install minikube-linux-amd64 /usr/local/bin/minikube

     curl -LO https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl
     chmod +x kubectl
     sudo mv kubectl /usr/local/bin/
     ```

3. **Start Minikube**:
   - Initializes the Minikube Kubernetes cluster on Linux.
     ```
     minikube start --driver=docker
     ```

### Verifying Installation:

1. **Verify Minikube**:
   - This command checks if Minikube is correctly installed and running.
     ```
     minikube status
     ```

2. **Verify kubectl**:
   - Ensures that `kubectl` is installed and can communicate with the Minikube cluster.
     ```
     kubectl version --client
     ```

3. **Interacting with the Cluster**:
   - A basic command to test if `kubectl` can communicate with and retrieve information from the Kubernetes cluster.
     ```
     kubectl get nodes
     ```

### Basic Commands:

1. **Starting, Stopping, and Deleting Minikube**:
   - Commands to control the lifecycle of your Minikube cluster: starting it, stopping it when not in use, and deleting it if needed.
     ```
     minikube start
     minikube stop
     minikube delete
     ```

2. **Accessing the Kubernetes Dashboard**:
   - This command opens the Kubernetes dashboard, a web-based user interface for managing Kubernetes clusters.
     ```
     minikube dashboard
     ```

## Deployment and Self-healing Demo

1. **Start Docker**:
   - Ensures that the Docker daemon is running, which is necessary for building Docker images and running containers.
     ```
     # Command to start Docker varies based on the platform
     ```

2. **Start MiniKube**:
   - Initializes a local Kubernetes cluster using Minikube, which is essential for deploying and testing Kubernetes applications.
     ```
     minikube start --driver=docker
     ```

3. **Point your Docker Daemon to the Minikube Instance**:
   - This step configures your Docker CLI to interact with the Docker daemon inside the Minikube instance. It’s essential for building Docker images that are accessible to your Kubernetes cluster.
   - Mac:
     ```
     eval $(minikube docker-env)
     ```
   - Windows:
     ```
     minikube docker-env | Invoke-Expression
     ```

4. **Build the Docker Image**:
   - Creates a Docker image from your application’s Dockerfile. This image is then used to run containers in your Kubernetes cluster.
     ```
     docker build -t esde_api .
     ```

5. **Create Deployment**:
   - Applies a deployment configuration to your Kubernetes cluster, effectively deploying your application.
     ```
     kubectl create -f ./my-deployment.yaml
     # or replace an existing deployment
     kubectl replace -f ./my-deployment.yaml
     ```

6. **Verify Deployment**:
   - Checks if the application has been successfully deployed and is running in the cluster.
     ```
     kubectl get deployments
     ```

7. **Port-forward Deployment**:
   - Forwards a local computer port to a port on a pod in your Kubernetes cluster. This allows you to access the application for testing.
     ```
     kubectl port-forward deployments/test-deployment-esde 3000:3000
     ```

8. **Test the Deployment**:
   - Access the deployed application to ensure it’s running correctly.
     ```
     curl http://127.0.0.1:3000
     # or visit in a browser
     ```

## Autoscaling Demo

9. **Break out of the port-forwarding loop**:
   - Stops the ongoing port-forwarding process to proceed with the next steps.
     ```
     # Use Ctrl + C to stop the process
     ```

10. **Enable the metrics-server**:
    - Activates the metrics-server addon in Minikube. This server collects and provides metrics for autoscaling purposes.
     ```
     minikube addons enable metrics-server
     ```

11. **Create Horizontal Pod Autoscaler (HPA)**:
    - Sets up an HPA for your deployment. This HPA will automatically scale the number of pods based on CPU usage.
     ```
     kubectl autoscale deployment test-deployment-esde --cpu-percent=5 --min=1 --max=10
     ```

12. **Check if HPA has been created**:
    - Verifies that the HPA has been successfully created and is monitoring your deployment.
     ```
     kubectl get hpa
     ```

13. **Port-forward the Deployment again** and **simulate load**:
    - Re-establishes a port-forwarding connection and generates artificial load. This is to demonstrate how the HPA scales the deployment in response to increased traffic.
    - Mac/Linux:
      ```
      kubectl port-forward deployments/test-deployment-esde 3000:3000
      while true; do curl http://127.0.0.1:3000; done
      ```
    - Windows:
      ```
      kubectl port-forward deployments/test-deployment-esde 3000:3000
      while ($true) { curl http://127.0.0.1:3000; }
      ```

14. **Monitor the HPA**:
    - Observes the behavior of the HPA as it scales the number of pods in response to the simulated load.
     ```
     kubectl get hpa
     ```

15. **Observe the scaling behavior**:
   - You can see the HPA in action, increasing the number of pods as the CPU load crosses the specified threshold.

16. **Stop the load**:
   - Terminates the load simulation, allowing the HPA to scale down the number of pods as the demand decreases.
     ```
     # Use Ctrl + C to stop the loop
     ```
