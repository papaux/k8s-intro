# Getting started

This guide shows how you can easily get started with kubernetes, running a small local lab environment.

This guide has been created and tested on Linux, but it should work on Windows and MacOS as well.

Alternatively, you can access a live kataconda playground here: https://www.katacoda.com/courses/kubernetes/playground

## Install local tools

For the local install, we need two tools to get started:
* `kubectl`, the swiss army knife to interact with kubernetes
* `minikube`, a tool that runs a single-node Kubernetes cluster in a virtual machine on your personal computer.

1. Install kubectl: https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/

2. Install minikube: https://v1-18.docs.kubernetes.io/docs/tasks/tools/install-minikube/

## Start a lab kubernetes

Start minikube:
```
minikube start
```

This should auto configure everything you need.
Time to test the cluster:
```
kubectl cluster-info
```

Open web browser with a dashboard showing the cluster status:
```
minikube dashboard
```

# kubectl

Basics about `kubectl`, the main tool for interacting with a K8's cluster.

Get cluster info:
```
kubectl cluster-info
```

Get nodes info:
```
kubectl get nodes
```

Get pods info:
```
kubectl get pods
```

Describe pod:
```
kubectl describe pod myapp
```

Getting help:
```
kubectl explain pods
```

# First steps with k8s

## Create resources

There are two options to create resources in a Kubernetes cluster, _imperative_ commands or using _declarative_ YAML files.


### Description files

When using the _declarative_ approach, all resources are managed with YAML files.

Kubernetes yaml files always contain these minimal set of information:
```
apiVersion: apps/v1
kind: Deployment

metadata:

spec:
```

Note that these YAML files describe the intended state of a system. When you update a file and deploy it, kubernetes will do whatever is required to bring your deployment to the intended state.

### Imperative create commands

Using imperative commands is much more limited that using YAML files, but allows to quickly and easily create resources.

```
kubectl create -h
```

## Pod

A pod is the first and simplest object we can create in a k8s cluster. It defines an application, made of one or more containers.

## Deployment

A deployment is a description of your application pods and intended "replicas", i.e. the number of running instances of this pod.

# Let's deploy a web app!


## Create a deployment

Enough about the introduction, let's do something with our cluster!

We will deploy a small web application based on an existing docker image using the imperative commands.

We create a new deployment called "demo" using the docker image "nginxdemos/hello":
```
kubectl create deployment --save-config --image=nginxdemos/hello demo
```

List the resources:
```
kubectl get deployment
kubectl get pods
```

Get the details about our deployment:
```
kubectl describe deployment demo
```


## Scale our web application

Want to scale your application with more replicas ? Easy peasy!

```
kubectl scale deployment demo --replicas 4
```

See the new state:

```
kubectl describe deployment demo
```

```
kubectl get pods
```

## How to access our application externally ?

We need to create a "Service" resource to expose our application to the world.

```
kubectl get service
```

Notice the existing service 

```
kubectl expose deploy demo --type="NodePort" --port 80
```

Check the services:

```
kubectl get service
```

Test our application. Currently it is only exposed internally on the node. But kubectl can be used to proxy the requests:

```
kubectl proxy
curl localhost:8001/api/v1/namespaces/default/services/demo/proxy/
```

## Service

Export as a LoadBalancer service instead:

```
kubectl delete demo
kubectl expose deploy demo --type="LoadBalancer" --port 80
```

```
minikube service demo
```

## Change the container ?

Remember the _declarative_ way to handle kubernetes resources ? Let's try it!

```
kubernetes get deployment demo -oyaml > demo-deployment.yaml
```

Change the image name in the deployment:
```
vim demo-deployment.yaml
```

Apply the changes:
```
kubectl apply -f demo-deployment.yaml
```

## Create a YAML template

```
kubectl run bee --image=nginx --dry-run=client -o yaml > pod.yaml
```

# Resources

* Same lab with kataconda: https://kubernetes.io/fr/docs/tutorials/hello-minikube/
* More hands-on exercises on K8s, from your browser: https://www.katacoda.com/courses/kubernetes