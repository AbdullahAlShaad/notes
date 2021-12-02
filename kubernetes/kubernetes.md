# Kubernetes Notes

### Basic Terminologies :

**Pods:** Pod is group of containers. Pod can group together can container images into a single deployable unit. Most of times
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

Basic `yaml` file for Kubernetes Deployment object

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80

```

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
