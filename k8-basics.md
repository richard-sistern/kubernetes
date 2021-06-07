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

## Examples

