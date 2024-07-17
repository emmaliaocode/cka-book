# Cluster Architecture

The purpose of Kubernetes is to host applications in the form of containers to deploy the applications and enable communications between different services easily. Many things are involved in making these happen.

## Kubernetes Architecture in Anology

Since a Kubernetes cluster hosts applications in "container" form, let's use the control ships and cargo ships as a metaphor for better understanding the basic components in the Kubernetes world.

### Master Node

A master node (aka Control Plane Node) is like a control ship, which is responsible for:

1. storing information about all the ships - ETCD
2. identifying which cargo ship to load the container, like a crane - Scheduler
3. monitoring and tracking the status of ships and containers, like various offices and departments - Controller Manager
4. orchestrating all operations mentioned above - API Server

### Worker Node

A worker node is like a cargo ship, which carries the containers and is responsible for:

1. liaising with the control ships and listen for the instructions, like a captain - Kubelet
2. communicating between cargo ships, like a communication device - Kube Proxy