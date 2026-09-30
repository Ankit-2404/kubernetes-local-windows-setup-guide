# Kubernetes Command Cheat Sheet

## Verify tools

``` powershell
docker version
kind version
kubectl version --client
```

## kind

``` powershell
kind get clusters
kind create cluster --name k8s-learning
kind delete cluster --name k8s-learning
```

## Context

``` powershell
kubectl config get-contexts
kubectl config current-context
kubectl config use-context kind-k8s-learning
```

## Cluster

``` powershell
kubectl cluster-info
kubectl cluster-info --context kind-k8s-learning
kubectl get nodes
kubectl get pods -A
```

## Workloads

``` powershell
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>

kubectl get deployments
kubectl describe deployment <deployment-name>
kubectl scale deployment <deployment-name> --replicas=5
```

## YAML

``` powershell
kubectl apply -f file.yaml
kubectl delete -f file.yaml
```

## Docker + kind

``` powershell
docker build -t my-app:1.0 .
kind load docker-image my-app:1.0 --name k8s-learning
```
