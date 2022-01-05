# Programming Kubernetes

### Controllers and Operators
A controller implements a control loop, watching the shared state of the cluster through
API server and making changes in an attempt to move the current state toward the desired
state.

Operators are controllers that encode some operational knowledge such as application
lifecycle management, along with the custom resource.

***The Control Loop*** : The control loop has three states.

- Read Resource State
- Change the state of objects
- Update the status of the resource

![alt text](https://github.com/Shaad7/notes/blob/master/images/k8s-control-loop.png?raw=true
"Kubernetes Controller")