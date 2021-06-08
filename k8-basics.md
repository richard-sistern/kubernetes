# Kubernetes Basics

## Commands

```shell
kubectl version

# Client Version: version.Info{Major:"1", Minor:"19", GitVersion:"v1.19.7", GitCommit:"1dd5338295409edcfff11505e7bb246f0d325d15", GitTreeState:"clean", BuildDate:"2021-01-13T13:23:52Z", GoVersion:"go1.15.5", Compiler:"gc", Platform:"windows/amd64"}
# Server Version: version.Info{Major:"1", Minor:"19", GitVersion:"v1.19.7", GitCommit:"1dd5338295409edcfff11505e7bb246f0d325d15", GitTreeState:"clean", BuildDate:"2021-01-13T13:15:20Z", GoVersion:"go1.15.5", Compiler:"gc", Platform:"linux/amd64"}
```

A master decides what will run on the nodes.  To show what nodes are available:

```shell
kubectl get nodes

# NAME             STATUS   ROLES    AGE    VERSION
# docker-desktop   Ready    master   162m   v1.19.7
```

### Deployment

Create a deployment:

```shell
# Previous Command
# kubectl run kubernetes-first-app --image=gcr.io/google-samples/kubernetes-bootcamp:v1 --port=8080

kubectl create deployment kubernetes-first-app --image=gcr.io/google-samples/kubernetes-bootcamp:v1 --port=8080

# deployment.apps/kubernetes-first-app created
```

Check a [deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) was created:

```shell
kubectl get deployments

# NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
# kubernetes-first-app   1/1     1            1           12s
```

Check the rollout status of a deployment:

```shell
kubectl rollout status deployment/kubernetes-first-app

# deployment "kubernetes-first-app" successfully rolled out
```

The deployment places the container and app inside a [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/), which represents the smallest deployable unit.  These run within a private isolate network, which is visible to other pods and services but cannot be accessed outside the network.

### Proxy

To expose an HTTP API:

```shell
kubectl proxy

# Starting to serve on 127.0.0.1:8001
```

Which can be queried with `curl`:

```shell
curl http://localhost:8001/version
{
  "major": "1",
  "minor": "19",
  "gitVersion": "v1.19.7",
  "gitCommit": "1dd5338295409edcfff11505e7bb246f0d325d15",
  "gitTreeState": "clean",
  "buildDate": "2021-01-13T13:15:20Z",
  "goVersion": "go1.15.5",
  "compiler": "gc",
  "platform": "linux/amd64"
}
```

### Pods

List Pod names with:

```shell
kubectl get pods

# NAME                                    READY   STATUS    RESTARTS   AGE
# kubernetes-first-app-76f586cc68-6bklf   1/1     Running   0          17m
```

To learn more about a specific pod:

```shell
curl http://localhost:8001/api/v1/namespaces/default/pods/kubernetes-first-app-76f586cc68-6bklf
```

To view a Pods logs:

```shell
kubectl logs kubernetes-first-app-76f586cc68-6bklf

# Kubernetes Bootcamp App Started At: 2021-06-07T18:52:35.034Z | Running On:  kubernetes-first-app-76f586cc68-6bklf
```

To view a pods environment variables:

```shell
kubectl exec kubernetes-first-app-76f586cc68-6bklf env
```

To access a container:

```shell
kubectl exec -ti kubernetes-first-app-76f586cc68-6bklf bash
```

### Service

A `service` in Kubernetes is an abstraction which defines a logical set of Pods and policy by which to access them.

Check existing Pods:

```shell
kubectl get pods

# NAME                                    READY   STATUS    RESTARTS   AGE
# kubernetes-first-app-76f586cc68-6bklf   1/1     Running   0          47m
```

List services:

```shell
kubectl get services

# NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
# kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   3h44m
```

Create a service:

```shell
kubectl expose deployment/kubernetes-first-app --type="NodePort" --port 8080
# service/kubernetes-first-app exposed
```

Use `get services` to provide the node port, alternativly:

```shell
kubectl get services/kubernetes-first-app -o go-template='{{(index .spec.ports 0).nodePort}}'

# 31849
```

To determine a Pod's cluster IP:

```shell
kubectl get pod -o wide
```

#### Labels

View labels on a deployment:

```shell
kubectl describe deployment

# Labels:  app=kubernetes-first-app
```

Query pods with the label:

```shell
kubectl get pods -l app=kubernetes-first-app

# NAME                                    READY   STATUS    RESTARTS   AGE
# kubernetes-first-app-76f586cc68-6bklf   1/1     Running   0          58m
```

Query services with the label:

```shell
kubectl get services -l app=kubernetes-first-app

# NAME                   TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
# kubernetes-first-app   NodePort   10.109.200.145   <none>        8080:31849/TCP   8m10s
```

Add a label:

```shell
kubectl label pod kubernetes-first-app-76f586cc68-6bklf ver=v1

# pod/kubernetes-first-app-76f586cc68-6bklf labeled

kubectl describe pods kubernetes-first-app-76f586cc68-6bklf

# Labels: app=kubernetes-first-app
#         pod-template-hash=76f586cc68
#         ver=v1
```

#### Remove

```shell
kubectl delete service -l app=kubernetes-first-app

# service "kubernetes-first-app" deleted

kubectl get services
```

### Scaling

To scale a deployment:

```shell
kubectl get deployments

# NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
# kubernetes-first-app   1/1     1            1           24h

kubectl scale deployments/kubernetes-first-app --replicas=4 

# deployment.apps/kubernetes-first-app scaled

kubectl get deployments

# NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
# kubernetes-first-app   4/4     4            4           24h
```

Additional information on a scaling operation:

```shell
kubectl describe deployments/kubernetes-first-app

# Events:
#   Type    Reason             Age   From                   Message
#   ----    ------             ----  ----                   -------
#   Normal  ScalingReplicaSet  2m2s  deployment-controller  Scaled up replica set kubernetes-first-app-76f586cc68 to 4
```

