# Kubernetes Notes

### Basic Terminologies :

**Pods**
: Pod is group of containers. Pod can group together can container images into a single deployable unit. Most of times
one container is run in one pod unless there is dependency.

**Node:**  Kubernetes runs workload by placing container into Pods to run on Nodes.
A node may be a  virtual or physical machine depending on the cluster.

**Cluster:** A kubernetes cluster is set of nodes running an applications.
Kubernetes cluster consist of one master node and set of worker nodes. These nodes can be either
physical computers or virtual machines. The master node controls the state of the cluster.

**Services:** Kubernetes services provide load balancing, naming and discovery to isolate one microservice from another

**Namespace:** Namespace provide isolation and access control, so that each microservice can control the degree to which other services interact with it
. Namespace is a way to a Kubernetes user to organize different clusters within just one physical cluster.

### K8S Architecture

Kubernetes cluster have two types of nodes.
1. **Master Node**
2. **Worker Node**

![alt text](https://github.com/Shaad7/notes/blob/master/images/K8S_Archi.png?raw=true 
"Kubernetes Architecture")

**API Server:** Users interact with k8s cluster via API Server in master node. Client of this api server can
be Kubernetes Dashboard(UI) or Kubectl(CLI) or any script.

**Scheduler:** Kubernetes Scheduler assigns pods to different nodes. It checks the requirements of pods and find 
a node which meets the requirement to run the pod. It also schedules nodes so that the pods are distributed 
among all the nodes.

**Controller Manager:** Control Manager is responsible for overall health of the cluster. It ensures correct
number of pod running as needed.

**ETCD:** It is called brain of the cluster. It is a key-value storage system. It stores the current state of 
the cluster. It stores nodes, controller and all the state related things. Any component of cluster can query etcd.

**Kubelet:** Kubelet is responsible for running the pods in a worked node. When scheduler schedules node, kubelet
runs the node according to pod spec. It tries to create a new pod if pod fails. If it can not 
restart failed pod, master detects a node failure and schedule the pod in another node.

**Kube-proxy:** Kube-proxy is responsible for maintaining network across the cluster. It maintains internet among
nodes, pods and containers.

**Container-Runtime (Docker):** It runs the container inside the pod.

### Kubernetes Objects

**Namespace** provides a degree of isolation withing the cluster. Namespace-based scoping 
is applicable only for namespaced objects(i.e. Deployments, Services, etc) and not for cluster
-wide objects(e.g. StorageClass, Nodes, PersistentVolumes, etc).Each Kubernetes resources
can only be in one namespace.

**Label:** Labels are key-value pairs that are attached to objects such as pods. Labels are intended to be used
to identifying attributes of objects that are meaningful and relevant to users.

**Label selectors :** Using label selector, the client/user can identify a set of objects.

**Field Selectors :** Used to select k8s resources based on the value of one or more resource fields.

To get all the running pods
```shell
kubectl get pods --field-selector status.phase=Running
```
**Finalizers :** Finalizers are namespaced keys that tell k8s to wait specific conditions are met before it fully 
deletes resources marked for deletion. It can be used as garbage collector


### Pods

Pods are the smallest deployable unit in kubernetes. A pod usually runs one container but can also run multiple container
that needs to work together. A pod natively provides shared networking and storage for its containers.

Each Pod is assigned a unique IP address. Containers inside a Pod can communicate between each other using 
`localhost`. Containers are not in same Pod can communicate using Pods IP addresses.

*Pod is not a process but environment for running containers*

Usually we don't need to create Pod manually. Deployment, StatefulSet and DaemonSet manages pods.
PodTemplates are specification for creating Pods and are included in workload resources such as Deployment,
StatefulSet, DaemonSet.

When the pod template is updated, the controller creates new pod based on updated template and replace the
existing one.

**Init Container**
: Init container runs in pod before actual container. Init container usually contains utilities or set up scripts
not present in an app image.

**Pod Topology Spread Constraint** 
: Using `topologyKey` we can put pods on different zones and nodes.

**Pod Termination** 
: API Server updates the pod status and the pod in the api server is considered dead beyond grace period(30s). Kubelet
notices the pod updates and start graceful shutdown. Control Plane removes the shutting-down Pod from Endpoints. The 
pod can no longer provide service. Other objects no longer consider the pod valid. When the graceful shutdown period
is over, kubelet triggers forcible shutdown.

**Pod Disruption Budget**
: Pod disruption budget tries to ensure a minimum number of pod running always. It prevents voluntary disruption
such a directly deleting a pos or updating a deployment pod template causing restart. However, it can not 
prevent involuntary disruption such as hardware failure or kernel panic. Though it counts both disruption.

### Node

Node in a kubernetes cluster is a physical or virtual machine which holds the pods. There are two ways to add nodes in API
server 
1. The kubelet on a node self-registers to the control plane
2. Human user manually add a Node object

***Heartbeats*** sent by k8s nodes to determine health of each node. There are two form of heartbeats.
1. updates to the `.status` of Node
2. updates Lease objects within the kube-node-lease namespace. Each node has an associated Lease Object

**Graceful Node Shutdown**
: kubelet ensures tha pod follows the normal pod termination process. During graceful shutdown kubelet terminates pod in 
two phases , termination of regular pods and critical pods. There is specific time duration to shut down critical 
pods.

**Controller**
: A controller tracks at least one k8s resource type, and it is responsible for making the current state of the object
come closer to desired state. Controller might carry the action out itself or will send messages to the API server 

**Cloud Controller Manager**
: The could-controller-manager is a k8s `control plane` component that embeds cloud specific control logic.
It lets us link our cluster into cloud provides api.

**Garbage Collection**
: Kubernetes checks for and deletes objects that no longer have owner references, like the pods left behind when
ReplicaSet is deletes. We can control how and when garbage collection deletes resources that have owner references
using Kubernetes finalizers.

1. ***Foreground cascading deletion***: k8s API Server first removes the dependents, then the owner.
2. ***Background cascading deletion***: k8s API Server first removes the owner, then removes the orphan objects.

_The kubelet only garbage collects the containers it manages._

### Deployment

A deployment provides declarative updates for Pods and ReplicaSets. We can create, update, delete and manage Pods or
ReplicaSets using Deployment.

To apply deployment
```shell
kubectl apply -f FilePath
```

To get all the deployment : 
```shell
kubectl get deployment
```

To update the image of the pods the deployment created 
```shell
kubectl set image deployment/nginx-deployment nginx=nginx:1.16.1
```

To check the revisions of nginx deployment
```shell
kubectl rollout history deployment/nginx-deployment
```

To see details of revision 2
```shell
kubectl rollout history deployment/nginx-deployment --revision=2
```

To rollback to a specific version we can use `--to-revision=2`
```shell
kubectl rollout undo deployment/nginx-deployment --to-revision=2
```

To scale a deployment 
```shell
kubectl scale deployment/nginx-deployment --replicas=10
```

### ReplicaSet

ReplicaSet is used to maintain a specific number of identical Pods running at any given time. ReplicaSet creates or
acquires nodes according to the manifest. ReplicaSet maintains owner relation with its Pods.

_A ReplicaSet only ensures a certain number of Pods are running at any given time. However, Deployment manages
ReplicaSet and provides other useful feature such as rollback. So most of the time we use Deployment_

If newly created Pods(from manifest) do not have a Controller as their owner reference and match the selector of a
ReplicaSet, the Pods will be immediately acquired by ReplicaSet.

***Pod Deletion Cost*** 
: We can assign an integer value cost with each pod. When scaling down the pod with lower cost will be removed first.

### StatefulSets

StatefulSet is used to manage a set of Pods which runs stateful applications like database.
StatefulSet maintains sticky identity for each Pod and the Pods are not interchangeable. 
If an app requires Stable,unique network identifiers and/or persistent storage and/or ordered 
deployment/scaling and/or automated rolling updates, StatefulSet is used.
