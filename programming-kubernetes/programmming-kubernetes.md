# Programming Kubernetes

### Controllers and Operators
A controller implements a control loop, watching the shared state of the cluster through
API server and making changes in an attempt to move the current state toward the desired
state.

Operators are controllers that encode some operational knowledge such as application
lifecycle management, along with the custom resource.