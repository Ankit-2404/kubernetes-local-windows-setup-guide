# Troubleshooting --- Windows + Docker + kind + kubectl

## 1. kind is not recognized

``` powershell
where.exe kind
```

If using C:`\Tools`{=tex}:

``` powershell
Get-Item "C:\Tools\kind.exe"
```

Make sure C:`\Tools `{=tex}is in your user PATH, then restart
PowerShell.

## 2. kubectl is not recognized

``` powershell
winget install -e --id Kubernetes.kubectl
```

Restart PowerShell and test:

``` powershell
kubectl version --client
```

## 3. Docker is unavailable

``` powershell
docker version
docker info
```

Start Docker Desktop and retry.

## 4. Wrong kubectl context

``` powershell
kubectl config get-contexts
kubectl config use-context kind-k8s-learning
kubectl config current-context
```

## 5. Cluster does not exist

``` powershell
kind get clusters
kind create cluster --name k8s-learning
```

## 6. Recreate resources after recreating the cluster

Your YAML files are outside the cluster:

``` powershell
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

## 7. Get more detail from kind

``` powershell
kind create cluster --name k8s-learning --verbosity 5
```
