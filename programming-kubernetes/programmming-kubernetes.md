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


