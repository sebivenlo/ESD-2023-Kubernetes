[Source](https://learn.microsoft.com/en-us/training/modules/intro-to-kubernetes/2-what-is-kubernetes)

## Kubernetes to Turn Containers into Microservices

Kubernetes enables developers to easily scale their applications up and down by spinning up more containers or decreasing the total number of containers being run.

## What is Container Management?

Container management consists of organizing, adding, removing, or updating a significant number of containers.

An example drone-tracking app consists of multiple microservices responsible for tasks like:

1. Caching
2. Data processing
3. Queuing

Each of these services is hosted in a container and can be deployed, updated, and scaled independently from each other.

## Container Orchestration

An orchestration system can automatically deploy and manage containers. It can automatically increase or decrease the number of deployed containers or ensure that all deployed containers are updated to a new version if an update is available.

## Define Kubernetes

Kubernetes is a portable, extensible open-source platform for managing and orchestrating containerized workloads.

The main benefits of Kubernetes are based on the abstraction of tasks such as:

1. Self-healing of containers; for example, restarting containers that fail or replacing containers.
2. Scaling deployed containers up or down dynamically, based on demand.
3. Automating rolling updates and rollbacks of containers.
4. Managing storage.
5. Managing network traffic.
6. Storing and managing sensitive information such as usernames and passwords.

## Considerations

- Aspects such as deployment, scaling, load balancing, logging, and monitoring are all optional; you are responsible for finding the best solution that fits your needs.
- If your app can run in a container, then it can run on Kubernetes.
- Cloud services such as Azure Kubernetes Services reduce many challenges by providing an environment.
