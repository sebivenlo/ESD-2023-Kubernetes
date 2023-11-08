## What is a Service and When Do We Need It?

Kubernetes makes use of pods, and each pod has its own IP address.

Pods are ephemeral and are destroyed frequently.

With the recreation of a pod, they will get a new IP address.

To make sure that the IP address stays (even if the pod dies), you need to add a service to it. The service keeps the IP address and can access the pod.

Services are also great for balancing the load.

Clients can call a single stable IP address instead of calling each pod individually.

Services are a good abstraction for loose coupling and for communication within the cluster. This applies to cluster components (pods) and external services, such as browser requests coming to the cluster or when you are talking to an external database.

So, a service is a sort of pointer towards the pods based on the use of labels. Services are not node-specific and can point to a pod regardless of where it runs in the cluster at any given moment in time. This can be achieved by exposing the service IP address as well as a DNS service name. Now, the application can be reached by either method as long as the service exists.

## Four Different Types of Services in Kubernetes

In Kubernetes, there are four different types of services:

1. **ClusterIP:** Exposes a service that is only accessible from within the cluster.

2. **NodePort:** Exposes a service via a static port on each node's IP.

3. **LoadBalancer:** Exposes the service via the cloud provider's load balancer.

4. **ExternalName:** Maps a service to a predefined `externalName` field by returning a value for the CNAME record.

### ClusterIP

The most common service type in Kubernetes is `ClusterIP`. A `ClusterIP` is used to expose a service on an IP address internal to the cluster, and access is only permitted from within the cluster. This is the default type used if you don't explicitly specify a type for a service. You can expose the service to the public internet using an Ingress or a Gateway.

By default, this service type assigns an IP address from a pool of reserved IP addresses within your cluster. If you set the `.spec.clusterIP` to "None," Kubernetes does not assign an IP address.

You have the option to specify your own cluster IP address when creating a service. To do this, set the `.spec.clusterIP` field. This is useful when you have existing DNS entries to reuse or legacy systems that require specific IP addresses that are difficult to reconfigure.

The IP address you choose must be a valid IPv4 or IPv6 address within the `service-cluster-ip-range` CIDR range configured for the API server. If you attempt to create a service with an invalid `clusterIP` address, the API server will return a 422 HTTP status code to indicate an issue.

## NodePort

The `NodePort` service type exposes the service on each node's IP at a static port known as the NodePort. When you use a `NodePort`, Kubernetes sets up a cluster IP address just like if you had requested a service of type `ClusterIP`.

Using a `NodePort` provides you with the flexibility to set up your own load balancing solution, configure environments that may not be fully supported by Kubernetes, or even directly expose the IP addresses of one or more nodes.

For a `NodePort` service, Kubernetes allocates an additional port (TCP, UDP, or SCTP to match the protocol of the service). Every node in the cluster configures itself to listen on the assigned port and forwards traffic to one of the ready endpoints associated with the service. You can access the `NodePort` service from outside the cluster by connecting to any node using the appropriate protocol (e.g., TCP) and the assigned port for that service.

## LoadBalancer

The `LoadBalancer` service type is used to expose a service externally using an external load balancer. Kubernetes itself does not directly offer a load balancing component; you must either provide one or integrate your Kubernetes cluster with a cloud provider.

Each cloud provider typically has its own native load balancer implementation. When you create a `LoadBalancer` service, Kubernetes will automatically create and use the cloud provider's external load balancer to route traffic to the associated services, including NodePort and ClusterIP services.

![Image](../../images/michiel/load_balancer.png)

*Image 1: Load balancer service type*

Instead of a NodePort type, we have a LoadBalancer. Similarly, we have the port of the service, which belongs to the ClusterIP. We also have the NodePort, which is the port that opens on the worker node. However, it's not directly accessible externally but only through the LoadBalancer itself. The entry point becomes a LoadBalancer first, and it can then direct the traffic to the NodePort on the worker node and the ClusterIP of the internal service. This is how the flow would work with the LoadBalancer service *(See image 1)*.

For example, the LoadBalancer service type is an extension of the NodePort type, which itself is an extension of the ClusterIP type.

## ExternalName

The `ExternalName` service type maps the service to the contents of the `externalName` field, such as the hostname `api.foo.bar.example`. This mapping configures your cluster's DNS server to return a CNAME record with that external hostname value. No proxying of any kind is set up.

Services of type `ExternalName` map a service to a DNS name. For example, when looking up the host `my-service.prod.svc.cluster.local`, the cluster DNS service returns a CNAME record with the value `my.database.example.com`. Accessing `my-service` works in the same way as other services, but with the crucial difference that redirection happens at the DNS level rather than through proxying or forwarding. If you later decide to move your database into your cluster, you can start its pods, add appropriate selectors or endpoints, and change the service's type.

### How to Define an ExternalName Service in Kubernetes

When setting up a service, if you do not specify its type, it will default to `ClusterIP`. Services are defined in a YAML (YML) file like all other Kubernetes objects.

Suppose you deployed pods running a back-end service to process data coming from a web front end. If you want to expose a service named 'service-backend' for the 'deployment-backend,' you would use:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: service-backend
spec:
  ports:
    - port: 4000
      protocol: TCP
      targetPort: 333
  selector:
    run: deployment-backend
  type: ClusterIP
```

With these settings you can create the ‘service-backend’, and any pod in the cluster can access it on the port 333 via http://service-backend:4000, or at the cluster’s IP address using port 4000. 

## Networking

### How to Allocate Pods Without Getting Conflicts

At its core, Kubernetes networking operates based on a fundamental concept: each pod has its own unique IP address, and this IP address is reachable from all other pods in the cluster. This is a fundamental concept of Kubernetes.

Now, you might wonder why having each pod with its own IP address is essential and valuable. The answer lies in the challenge of managing distributed infrastructure with multiple servers. The primary challenge is how to allocate ports to services and applications running on these servers without encountering conflicts. After all, you can only allocate a port once on a single host, and with containers, this challenge becomes prominent due to how container port mapping works.

For instance, consider a PostgreSQL container where inside the container, the Postgres application starts at port 5432. When starting containers directly on your machine, what you essentially do is bind your host port to the application port in the container.

![Image](../../images/michiel/bind_a_docker_container.png)

*Image 2: Code to start and bind a docker container.*

In Image 2, you can see how we map or bind the port on the host to the port of the application running inside the Docker container. An important note is that it doesn't have to be the same port; you can also assign it a completely different port. For example, the port number can be 5000. By doing this, the application is now reachable via the host port. 

Now, let's imagine that we have a running PostgreSQL container. We can start another PostgreSQL container that will also run at the same port, but we bind it to a different port (See Image 3). This is the fundamental concept of how container port mapping works.

![Image](../../images/michiel/binded_to_a_different_port.png)

*Image 3: Same port but it is binded to a different port.*

A common problem arises when you have hundreds of containers running on your servers: How can you keep track of available ports on the host for binding? With this type of port allocation, it becomes increasingly challenging to maintain an overview. 

Kubernetes addresses this problem by abstracting the container using "pods." A pod can be thought of as its own small machine with its own IP address, typically hosting one main container. 

For instance, consider a pod running a Postgres container. When a pod is created on a node, it gets its own network namespace and a virtual Ethernet connection to connect it to the underlying infrastructure network. Think of a pod as a host, much like your laptop. Both have IP addresses and a range of ports they can allocate to their containers. This eliminates the need to worry about port mappings on the server where the pod is running; you only need to manage port allocations inside the pod itself. Most of the time, a pod typically contains only one main container, or in some cases, a maximum of up to six containers. This minimizes the possibility of conflicts, as you have a clear overview of the containers running within each pod.

In practice, this means that you can have, for example, seven microservices applications, each running on port 8080 inside seven different pods, and you won't encounter conflicts because they all operate on self-contained, isolated machines.

![Image](../../images/michiel/micro_services.png)

*Image 4: 7 Micro services applications that all run on port 8080 inside 7 different pods.*

One valuable aspect of pod abstraction over individual containers is the flexibility it provides in replacing the container runtime in Kubernetes. To run pods, you need to install a container runtime on each node in the cluster, ensuring that pods can be executed. The Kubernetes configuration remains consistent because it operates at the pod level. Consequently, Kubernetes is not tightly bound to any specific container runtime implementation.

However, there are scenarios where a pod may contain two or more containers. In such cases, you need to run a helper or side application alongside your main application. For instance, you might require synchronization between multiple database pods or periodic backups of the application, which would involve a backup side container within your application. Additional containers in a pod can serve various purposes, such as acting as a scheduler or an authentication gateway. There are many use cases where you may end up with more than one container inside a pod.

![Image](../../images/michiel/multiple_containers.png)

*Image 5: Pod with multiple containers (Main - Helper)*

##How do these containers communicate with each other?

Don't forget that a pod is an isolated virtual host with its own network namespace, and all the containers inside the pod run within the same network namespace. This means that containers can communicate with each other using `localhost` and a port number, similar to running multiple applications on your local computer.

In a Kubernetes cluster, when you run Docker containers, there is a "pause" container in each pod, also known as a sandbox. This "pause" container is responsible for holding the network namespace for the pod. Kubernetes creates pause containers to obtain the pod's IP address and set up the network namespace for all other containers within that pod. As a result, a pause container facilitates communication between containers in the same pod.

For instance, if a container within a pod dies and a new one is created, the pod retains its IP address. However, it's essential to note that when the pod itself is terminated, a new pod will be created with a different IP address.

## Cites and references

Links:
- [Kubernetes Services explained](https://www.youtube.com/watch?v=T4Z7visMM4E)
- [Pods and Containers](https://www.youtube.com/watch?v=5cNrTU6o3Fw)

Websites
- https://www.vmware.com/topics/glossary/content/kubernetes-services.html 
- https://kubernetes.io/docs/setup/production-environment/container-runtimes/ 
- https://stackoverflow.com/questions/48651269/what-are-the-pause-containers 
- https://kubernetes.io/docs/concepts/services-networking/service/ 
