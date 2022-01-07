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

## Basics of client-go

***There are options structs for CRUD verbs like CreateOptions, GetOptions, UpdateOptions, DeleteOptions and
ObjectMeta. All of these are frequently extended with new features, we usually call them API
machinery features***

### Kubernetes Objects in Go

Kubernetes objects that are instances of a kind and are served as a resource by the API server 
are represented as structs. Kubernetes objects in Go is a data structure that can :
- Return and set the GroupVersionKind
- Be deep-copied

#### TypeMeta
TypeMeta describes an individual object in an API response or request with strings representing
the type of the object and its API schema version.

#### ObjectMeta
ObjectMeta contains various information about the resource like Namespace, UID, Resource Version, Creation
and Deletion Timestamp.

#### spec and status
spec is the user desire and status is the outcome of that desire usually filled by a controller in the system.


_A client set gives access to clients for multiple API groups and resources._

### Informers and Caching 

Informers five a higher-level programming interface for the most common use case for watches :
- in memory caching
- fast,indexed lookup of objects by name

Informer get input from the API server as events and register event handlers for adds, removes, and updates.
Informer also implement the in-memory cache using a store.

Informers allow event handlers for the three cases add, update and delete. These are usually used to
trigger the business logic of a controller. Informers are initiated using `shared informer factory`.

### Work Queue

A priority queue where items can be added and taken out. The basic queue is modified by adding special
feature like delaying add and rate limiting add.

### API Machinery in Depth
The API Machinery repository implements the basics of the Kubernetes type system. Type refers to kinds.

#### Kinds
Kinds do not formally map one-to-one to HTTP paths. Many kinds have HTTP REST endpoints that are used to 
access objects of the given kind. But there are also kinds without any endpoint. Kinds are singular and 
CamelCase.

#### Resources
Each GVR(GroupVersionResource) corresponds to one HTTP path. GVRs are used to identify REST endpoints of the
Kubernetes API. Resources are plural and lowercase.

#### REST Mapping
The mapping of a GVK to a GVR is called REST mapping.

#### Scheme
A scheme connects the world of Golang with the implementation-independent world of GVKs. The main
feature of a scheme is the mapping of Golang types to possible GVKs.

**Golang type--**(_Scheme_)**--> GroupVersionKind --**(RESTMapper)**-->GroupVersionResource--**(_client_) 
**--> HTTP path**

## Using Custom Resources

Custom resources are used for small, in-house configuration objects without any corresponding controller
logic - purely decoratively defined.

Custom resources are available in k8s cluster. They are stored in the same etcd instance as the main
Kubernetes API resources and served by the same Kubernetes API server. Requests fall back to 'apiextensions-api
server' which serves the resources defined via CRDs if they are not native Kubernetes resources or handled
by aggregated API servers.

A CustomResourceDefinition(CRD) is a Kubernetes resource itself. It describes the available CRs in the
cluster.

[_Sample YAML_](https://github.com/Shaad7/notes/blob/master/sample-yaml/resource-definiton.yaml)
to create Custom Resource Definition and [_Sample YAML_](https://github.com/Shaad7/notes/blob/master/sample-yaml/resource-definiton.yaml) to create a resource using that CRD.

### Validating Custom Resource

Custom Resources can be validated by the API server during creation and updates. This is done based on the
OpenAPI v3 schema specified in the validation fields in the CRD spec. More complex validations can be 
implemented in validating admission webhooks.

### Subresources

Subresources are special HTTP endpoints, using a suffix appended to the HTTP path of the normal resource.
Pods have a number of subresources such as /logs, /portforward, /exec and /status.

#### Status subresource
The /status subresource is used to split the user-provided specification of a CR instance from the 
controller-provided status. Status field is updated by Controller.

#### Scale subresources
The /scale subresource is a projective view on the resource, allowing us to view and modify replica
values only. Scale field is updated by user.

### Schema Options

#### Finalizers
Finalizers allow controllers to implement asynchronous pre-delete hooks. Custom objects support finalizers
similar to build-in objects.

### Validation
Custom resources are validated via OpenAPI v3 schemas. Additional validation using admission webhooks can
be done.

_We can also specify default values in OpenAI v3 validation schema. Default value is used when user don't
specify that field value or provides a null value._

### Clients

#### Dynamic Client
Used by Kubernetes itself for controllers that are generic, like the garbage collection controller.

#### Typed Clients
Typed clients use Golang types which are specific for each GVK. Kinds are represented as Golang structs.
Every Golang type corresponding to a GVK embeds the TypeMeta struct. ObjectMeta stores Name, Namespace and other
details.

#### controller-runtime client of operator SDK and Kubebuilder
Controller-runtime client is capable of handling any kind that is registered in a given scheme. It uses
discovery information from the API server to map the kinds to HTTP paths. Controller-runtime client 
presented is just one object for all kinds.

### Versions in CustomResourceDefinitions

Custom resource objects need to be able to be served at multiple versions. To achieve this, the custom
resource objects must sometimes be converted between the version the are stored at and the version they 
are served at. If the conversion involves schema changes and requires custom logic, a conversion webhook
should be used.