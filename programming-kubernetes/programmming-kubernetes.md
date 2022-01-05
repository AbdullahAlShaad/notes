# Programming Kubernetes

### Controllers and Operators
A controller implements a control loop, watching the shared state of the cluster through
API server and making changes in an attempt to move the current state toward the desired
state.

An Operator is an application-specific controller that extends the Kubernetes API to create, configure
and manage instances of complex stateful application on behalf of a Kubernetes user.
Operators are controllers that encode some operational knowledge such as application
lifecycle management, along with the custom resource.

***The Control Loop*** : The control loop has three states.

- Read Resource State
- Change the state of objects
- Update the status of the resource

![alt text](https://github.com/Shaad7/notes/blob/master/images/k8s-control-loop.png?raw=true
"Kubernetes Controller")

***Operator*** are consist of two things :
- A set of _Custom Resource Definitions_(CRDs) capturing domain-specific schema and custom resources 
following the CRDs that represents the domain of interest.
- A custom controller, supervising the custom resources, potentially along with core resources.

An operator has domain specific knowledge and knows how to do stuff on that domain. For example Cassandra 
operator knows when and how to re-balance nodes. An operator for service mash knows how to create a route.


## Kubernetes API Basics

A kubernetes cluster has master nodes and worker nodes. The Control plane on the master node consist 
of the API server, controller manager and scheduler. The API server reads and manipulates state.

### The HTTP Interface of the API Server

The API server HTTP interface handles HTTP requests to query and manipulate Kubernetes resources using
HTTP Verbs.
- GET verb to retrieve resource data
- POS verb for creating a resource
- PUT ver for updating an existing resource
- PATCH verb for partial updates of existing resource
- DELETE verb to destroy a resource

***API group*** : A collection of Kinds that are logically related.
***Resource*** : Identifies a set of HTTP endpoints exposing the CRUD semantics of a certain object 
type in system. For example /pods, /pods/nginx

### How the API Server Processes Requests

The HTTP request is processed by a chain of filters. It applies a series of filter operation on the 
request. If passed, each filter pass the request onto the next otherwise it returns an HTTP failed response.
Then the multiplexer routes the HTTP request to respective handler. It retrieves and delivers the request
object from etcd storage. Request for RESTful resources go into the request pipeline consisting of:
- admission : It has plugins that validate or mutate the request payload.
- validation : Incoming objects are checked against a large validation logic which exist for each 
object type. For example, names are checked if it is valid DNS name.
- etcd-backed CRUD logic : Different HTTP verbs are implemented in this stage.



