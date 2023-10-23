# Kubernetes
## Briefly explain containerization and why it's important. 
- "Containerization is a software deployment process that bundles an application’s code with all the files and libraries it needs to run on any infrastructure." (https://aws.amazon.com/what-is/containerization/)
- Benefit of containerization is that one can create a single software package, or container, that runs on all types of devices and operating systems (https://aws.amazon.com/what-is/containerization/)
- Other benefits are 
    - Agility ("use of agile and DevOps tools for rapid application development and enhancement")
    - Speed (lightweight containers, no overhead)
    - Fault Isolation (failure of one container does not affect other containers, easy to pinpoint source of faults)
    - Efficiency (shares OS kernel thus more efficient than a VM allowing for higher server efficiencies, reducing server and licensing costs)
    - Security (isolation of containers also prevents invasion of malicious code affecting other containers or host system, also configurable security permissions that allow even more)
    - Ease of Management (automation of installation, scaling and management of containerized workloads and services)
    - Source: https://www.ibm.com/topics/containerization
## Introduce Kubernetes and its role in container orchestration. 
- Kubernetes (also known as K8s) is an open-source container orchestration system originally designed by Google and now maintained by the Cloud Native Computing Foundation. (https://en.wikipedia.org/wiki/Kubernetes)
- Initially released in 2014 (https://en.wikipedia.org/wiki/Kubernetes)
- The name Kubernetes originates from Greek, meaning helmsman or pilot. K8s as an abbreviation results from counting the eight letters between the "K" and the "s". Google open-sourced the Kubernetes project in 2014. Kubernetes combines over 15 years of Google's experience running production workloads at scale with best-of-breed ideas and practices from the community. (https://kubernetes.io/docs/concepts/overview/)
- Today, Kubernetes is the most popular container orchestration platform, and most leading public cloud providers - including Amazon Web Services (AWS), Google Cloud Platform, IBM Cloud and Microsoft Azure - offer managed Kubernetes services. (https://www.ibm.com/topics/container-orchestration)
- Kubernetes works with many container engines, such as Docker, but it also works with any container system that conforms to the Open Container Initiative (OCI) standards for container image formats and runtimes. ((https://www.ibm.com/topics/containerization))
- Some of the most crticial Kubernetes Advantages are:
    - Automatic Bin packing
        - Kubernetes will automatically package your application and create container scheduling based on all available resources and requirements without sacrificing availability. As a result, Kubernetes will balance between best effort and critical workloads to save unused resources and ensure complete utilization.
    - Load Balancing & Service Discovery
        - Kubernetes provides peace of mind regarding networking and communication since it automatically assigns IP addresses to containers. In addition, for a set of containers, it gives a single DNS name that will load-balance traffic within the cluster. 
    - Storage Orchestration
        - Kubernetes allows you to choose the system storage you want to mount. You can opt for public cloud providers like AWS, GCP, or even local storage. Moreover, you can use shared networks storage systems like iSCSI, NFS, etc.
    - Self-Healing
        - Kubernetes is capable of an automatic restart of all containers that fail during execution. In addition, it will kill all containers that don’t respond to health checks previously defined by the user. Finally, if the node dies, it will reschedule and replace all failed containers in all other available nodes
    - Secret & Configuration Management
        - Kubernetes can assist you with updates and deployment of secrets and application configuration without rebuilding your image and exposing the secrets within the stack configuration.
    - Batch Execution
        - Besides managing services, Kubernetes can handle your batch and CI workloads, which will replace failed containers if need be
    - Horizontal Scaling
        - Kubernetes requires a single command to scale up the containers, but it can also scale them down with CLI. You can perform scaling via the Dashboard found in Kubernetes UI.
    - Automatic Rollbacks & Rollouts
        - Kubernetes can progressively roll out updates and changes to your app or its configuration. If something goes wrong, Kubernetes can and will roll back the change.
    - Source: https://www.clickittech.com/devops/kubernetes-architecture-diagram/

## Mention key Kubernetes components (Master, Node, Pod, Service). 
- API server: This component exposes via API the Kubernetes control plane. This is how the Kubernetes CLI or “kubectl” interacts with the cluster.
- The etcd: Etcd is a key/value store database where all the Kubernetes data gets stored and when all means like all of it.
- The scheduler: The scheduler watches for pods and finds them a node with enough resources to run.
- Then, the controller manager. This component is a single binary composed of four sub-processes and they are:
    - The node controller: Responsible for the health of the nodes in the cluster.
    - The replication controller: responsible for keeping the desired amount of pods for each replication.
    - The endpoints controller: handles the Endpoints object that links services and pods.
    - The service account and token controller: handles the default accounts and API access for namespaces.
- The last master component is the cloud controller manager, which handles all interactions with your cloud provider. It works similar to the regular controller manager, but just for the cloud provider. As you can see, controllers are very powerful but there is more. In future episodes, we will talk about to create your own controller.
- Source: https://medium.com/@cuemby/main-components-of-kubernetes-82c024ceea69
- Control plane
    - The collection of processes that control Kubernetes nodes. This is where all task assignments originate.
- Nodes
    - These machines perform the requested tasks assigned by the control plane.
- Pod
    - A group of one or more containers deployed to a single node. All containers in a pod share an IP address, IPC, hostname, and other resources. Pods abstract network and storage from the underlying container. This lets you move containers around the cluster more easily.
- Service
    - This decouples work definitions from the pods. Kubernetes service proxies automatically get service requests to the right pod—no matter where it moves in the cluster or even if it’s been replaced.
- Kubelet
    - This service runs on nodes, reads the container manifests, and ensures the defined containers are started and running.
- kubectl
    - The command line configuration tool for Kubernetes.
- Cluster
    - A working Kubernetes deployment.
- Source: https://www.redhat.com/en/topics/containers/what-is-kubernetes#speak-kubernetes
- Master Node
    - Master Node is the starting point for all administrative tasks, and its responsibility is managing the Kubernetes cluster architecture.
    - It’s possible to have more than one master node within the cluster, and what’s required for checking the fault tolerance as more master nodes will place the system in the mode known as “High Availability.”
    - However, one master node has the role of the main node that performs all the tasks.
- Pod 
    - A single application controlled by one or several containers. A pod contains a unique network ID, application containers, and storage resources to determine how it’ll run containers.
- Service 
    - Pods can easily suffer a change. Therefore, Kubernetes can’t assure that a physical pod will remain alive (if the replication controller ends and begins with new pods). 
    - Instead, the service will display a logical set of pods, but it will also play the part of a gateway. This means that you won’t have to keep track of pods that make up the service, as pods will be able to send requests to the service.
- Source: https://www.clickittech.com/devops/kubernetes-architecture-diagram/
## Provide a high-level overview of Kubernetes architecture. 
![Kubernetes Architecture Diagram](https://images.clickittech.com/2020/wp-content/uploads/2022/04/13202329/Diagram-55-1536x1288.jpg)
Source: https://www.clickittech.com/devops/kubernetes-architecture-diagram/



