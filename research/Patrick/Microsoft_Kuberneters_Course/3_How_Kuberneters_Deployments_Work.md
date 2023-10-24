**Source:** [https://learn.microsoft.com/en-us/training/modules/intro-to-kubernetes/4-how-app-deployments-work](https://learn.microsoft.com/en-us/training/modules/intro-to-kubernetes/4-how-app-deployments-work)

An example app has several components that are deployed separately from each other. If you use Kubernetes, then it's your job to configure the deployments on the cluster.

## Pod Deployment Options

When you use the `kubectl` command, you have several options to manage the deployment of pods, such as:

- Pod templates
- Replication controllers
- Replica sets
- Deployments

These fields make use of YAML to describe the intended state of the pod or pods to be deployed.

### What is a pod template?

A pod template describes the configuration of the pod you wish to deploy. The template contains information such as the name of the container image and which container registry to use to fetch the images. 

These templates are also defined using YAML in the same way you would define Docker files.

If you manually deploy a template, then it will not relaunch after it fails automatically. To manage the lifecycle of a pod, you need to create a higher-level Kubernetes object.

### What is a replication controller?

A replication controller uses pod templates and defines a specified number of pods that must run. The controller helps you run multiple instances of the same pod and ensures pods are always running on one or more nodes in the cluster.

The controller replaces running pods in this way with new pods if they fail, are deleted, or are terminated.
