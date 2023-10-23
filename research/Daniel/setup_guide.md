# Setting Up Minikube and Using kubectl

-----------------------------------------------

## Step-by-Step Guide to Setting Up Minikube:

## Windows Set Up:

1. **Install Chocolatey**:
   - Chocolatey is a package manager for Windows. It simplifies the installation of software on Windows machines.
   - Open a command prompt as an administrator and run the following command:
     ```
     Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
     ```

2. **Install Docker**:
   - Install Docker on your Windows machine using Chocolatey with the following command:
     ```
     choco install docker-desktop
     ```

3. **Install Minikube**:
   - Using Chocolatey, install Minikube with the following command:
     ```
     choco install minikube
     ```

4. **Install kubectl**:
   - kubectl is a command-line tool to interact with Kubernetes clusters.
   - Install it using Chocolatey:
     ```
     choco install kubernetes-cli
     ```

5. **Start Minikube with Docker Driver**:
   - Now that Hyper-V, Docker, and Minikube are installed, start Minikube using the Docker driver:
     ```
     minikube start --driver=docker --memory=2048
     ```

## MacOS Set Up:

1. **Install Homebrew**:
   - If you don't have Homebrew installed, you can install it with the following command:
     ```
     /bin/ -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
     ```

2. **Install Docker**:
   - Install Docker on your MacOS machine using Homebrew with the following command:
     ```
     brew install --cask docker
     ```

3. **Install Minikube and kubectl**:
   - Using Homebrew, install Minikube and kubectl:
     ```
     brew install minikube kubernetes-cli
     ```

4. **Start Minikube**:
   - Now that Docker, Minikube, and kubectl are installed, start Minikube with the following command:
     ```
     minikube start --driver=docker
     ```

## Linux Set Up:

1. **Install Docker**:
   - Install Docker on your Linux machine using the package manager of your distribution. For example, on Ubuntu/Debian, you can use the following commands:
     ```
     sudo apt-get update
     sudo apt-get install docker.io
     ```

2. **Install Minikube and kubectl**:
   - For Linux, you can download the latest Minikube and kubectl binaries from their respective GitHub releases pages or use the package manager of your distribution.
   - For Ubuntu/Debian, you can use the following commands to download and install Minikube and kubectl:
     ```
     curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
     sudo install minikube-linux-amd64 /usr/local/bin/minikube

     curl -LO https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl
     chmod +x kubectl
     sudo mv kubectl /usr/local/bin/
     ```

3. **Start Minikube**:
   - Start Minikube with the following command:
     ```
     minikube start --driver=docker
     ```

## Verifying Installation:

1. **Verify Minikube**:
   - To check that Minikube is installed properly, run the following command:
     ```
     minikube status
     ```

2. **Verify kubectl**:
   - To verify that kubectl is installed and configured properly, run:
     ```
     kubectl version --client
     ```

3.  **Interacting with the cluster**:
   - Use kubectl to interact with your cluster. For instance, to view nodes in the cluster, run:
     ```
     kubectl get nodes
     ```

     

## Basic Commands:

1. **Starting and Stopping Minikube**:
   - To start Minikube:
     ```
     minikube start
     ```
   - To stop Minikube:
     ```
     minikube stop
     ```
   - To delete the Minikube cluster:
     ```
     minikube delete
     ```

2. **Accessing the Kubernetes Dashboard**:
   - To access the Kubernetes dashboard, run:
     ```
     minikube dashboard
     ```

-------------------------------------------------------------

# Self-healing & Auto-scaling Demonstration Guide

In this chapter, we will explore self-healing and auto-scaling in Kubernetes to understand how Kubernetes ensures the desired state of your applications and can automatically adjust resources based on workload.

## Understanding Desired State in Kubernetes

### Introduction to Desired State

In Kubernetes, the desired state is a declaration of how the system should look and behave. This desired state is typically defined using YAML or JSON configuration files. These files specify what containers should run, how they should be networked, and other details.

### Creating a Simple Deployment

Let's start by creating a simple deployment using a basic nginx image. Nginx is a popular open-source web server and reverse proxy server. In this context, an "image" is a lightweight, stand-alone, executable package that includes everything needed to run a piece of software, including the code, runtime, libraries, and system tools. Kubernetes uses images to run containers.

```bash
kubectl create deployment nginx-demo --image=nginx
```

In this command:

- `kubectl create deployment` instructs Kubernetes to create a deployment object.
- `nginx-demo` is the name we've given to this deployment.
- `--image=nginx` specifies that we want to use the official nginx image from Docker Hub.

### YAML File Usage

Kubernetes uses YAML or JSON configuration files to define resources such as deployments, services, and pods. These configuration files provide a declarative way to specify what you want your Kubernetes cluster to do. Let's briefly explain how YAML files are used in this context:

1. **Deployment YAML**: When we run `kubectl create deployment nginx-demo --image=nginx`, Kubernetes generates a YAML file behind the scenes that describes the deployment. This YAML file includes information about the desired state, such as the container image to use and the desired number of replicas. You can view this YAML file using `kubectl get deployment nginx-demo -o yaml`.

2. **Resource Requests**: Resource requests, such as CPU and memory, can also be defined in a deployment YAML file. These requests help Kubernetes make scaling decisions. For example, you can specify that a container requires a certain amount of CPU, and Kubernetes will use this information to determine if it needs to scale the deployment based on resource usage. This will be shown later in a demonstration.

By understanding and using these YAML configuration files, you can effectively manage the desired state of your applications in Kubernetes, define resource requirements, and enable auto-scaling based on your application's needs.

### Demonstrating Desired State and Self-Healing in Action

Now, let's scale the deployment to have three replicas:

```
kubectl scale deployment nginx-demo --replicas=3
```

Check the deployment and pods:

```
kubectl get deployments
kubectl get pods
```

Notice that now there are three nginx pods running, matching the new desired state.

Now delete one of the pods manually, and see how it is recreated automatically:

```
kubectl delete pod
kubectl get pods
```

### Cleanup

Let's clean up the resources we created:

```
kubectl delete deployment nginx-demo
```

## Demonstrating Auto-Scaling in Kubernetes

### Introduction to Auto-Scaling

Kubernetes can automatically scale the number of pods based on certain metrics like CPU utilization or custom metrics. We'll introduce the Horizontal Pod Autoscaler (HPA), which automatically scales the number of pods in a deployment or replica set based on observed metrics.

### Setting Up Metrics Server

For HPA to work, you need the Metrics Server running in your cluster. If you're using Minikube, enable it:

```
minikube addons enable metrics-server
```

Wait a few minutes for the Metrics Server to collect data.

### Deploying a Sample Application

Deploy a sample application using the nginx image:

```
kubectl create deployment nginx-demo --image=nginx
```

Expose the deployment:

```
kubectl expose deployment nginx-demo --type=LoadBalancer --port=80
```

Important: Ensure that the deployment has resource requests set, especially for CPU. This is crucial for the HPA to make scaling decisions:

```
kubectl set resources deployment nginx-demo --requests=cpu=100m
```

### Setting Up Horizontal Pod Autoscaler (HPA)

Create an HPA that scales the nginx-demo deployment based on CPU utilization:

```
kubectl autoscale deployment nginx-demo --cpu-percent=30 --min=1 --max=10
```

This command means that if the CPU usage exceeds 30%, Kubernetes will try to scale out the number of pods, up to a maximum of 10 pods.

### Generating Load

To simulate traffic and increase CPU usage, you can use a tool like hey or wrk. For this example, let's use a simple infinite loop:

```
kubectl run -i --tty load-generator --image=busybox /bin/sh
```

Once inside the container:

```
while true; do wget -q -O- http://nginx-demo.default.svc.cluster.local; done
```

Let this run for a few minutes.

### Observing Auto-Scaling in Action

Monitor the HPA:

```
kubectl get hpa
```

You should see the CURRENT number of replicas increase as the CPU utilization goes up. Also, monitor the pods:

```
kubectl get pods
```

You'll notice more pods being created to handle the increased load.

### Cleanup

Let's clean up the resources we created:

```
kubectl delete deployment load-generator
kubectl delete hpa nginx-demo
kubectl delete deployment nginx-demo
kubectl delete service nginx-demo
```

This chapter provides a hands-on demonstration of self-healing and auto-scaling in Kubernetes, helping you understand how Kubernetes manages desired states and adapts to workload changes.
