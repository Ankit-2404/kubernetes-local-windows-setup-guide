# Kubernetes Local Practice on Windows --- Beginner Setup Guide

A beginner-friendly, step-by-step guide for setting up a local
Kubernetes practice environment on a Windows laptop **without using a
cloud Kubernetes platform**.

This guide is based on the setup path used while learning Kubernetes:

**Windows → Docker Desktop (Docker Engine) → kind → Kubernetes cluster →
kubectl → VS Code**

It also explains the important concepts behind the commands so that you
understand *why* you are running them.

------------------------------------------------------------------------

## 1. What You Will Build

``` text
                    Your Windows Laptop
                           |
                      Docker Desktop
                           |
                       Docker Engine
                           |
                          kind
                           |
                 +---------------------+
                 | Kubernetes Cluster  |
                 |                     |
                 | Control Plane Node  |
                 |                     |
                 |   Pods / Workloads  |
                 +---------------------+
                           ^
                           |
                        kubectl
                           ^
                           |
                         VS Code
```

You will be able to practice:

-   Pods
-   Deployments
-   ReplicaSets
-   Services
-   ConfigMaps
-   Secrets
-   Namespaces
-   Scaling
-   Self-healing
-   Rolling updates
-   Networking
-   Ingress
-   Kubernetes YAML
-   Docker images inside Kubernetes

------------------------------------------------------------------------

## 2. Important Definitions

### Docker

Docker is a platform for building, packaging, and running applications
in containers.

Think:

``` text
Application + dependencies
          |
        Docker
          |
      Container
```

Docker is mainly concerned with creating and running containers.

### Kubernetes

Kubernetes is a container orchestration platform.

It manages containers/workloads across one or more nodes and provides
features such as:

-   deployment
-   scaling
-   service discovery
-   self-healing
-   rolling updates
-   scheduling

### Kubernetes Cluster

A cluster is the complete Kubernetes environment managed as one system.

Conceptually:

``` text
Cluster
 |
 +-- Control Plane
 |
 +-- Worker Node(s)
      |
      +-- Pods
           |
           +-- Container(s)
```

### Node

A node is a machine/environment where Kubernetes workloads can run.

With kind, Kubernetes nodes are implemented as Docker containers.

### Pod

A Pod is Kubernetes' smallest deployable unit.

A Pod normally contains one application container, although a Pod can
contain multiple tightly coupled containers that share networking and
storage.

### Container

A container is the isolated runtime environment containing an
application and its dependencies.

### kind

`kind` means **Kubernetes IN Docker**.

It creates local Kubernetes clusters using Docker containers as
Kubernetes nodes.

This is especially useful for local learning and testing.

### kubectl

`kubectl` is the Kubernetes command-line tool.

It communicates with the Kubernetes API server and lets you:

-   create resources
-   inspect resources
-   view logs
-   scale applications
-   delete resources
-   troubleshoot workloads

### YAML

Kubernetes YAML files describe the desired state of resources.

Example:

``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
```

This says that the desired resource is a Deployment named `nginx` with
three replicas.

### Cluster vs Node vs Pod vs Container

Remember this hierarchy:

``` text
Kubernetes Cluster
        |
        +-- Node
             |
             +-- Pod
                  |
                  +-- Container
```

------------------------------------------------------------------------

# 3. Why Use kind Instead of Docker Desktop Kubernetes?

Docker Desktop can provide a local Kubernetes cluster, but for this
learning setup we use **kind**.

With kind:

``` text
Docker Desktop
      |
      +-- Docker Engine
             |
             +-- kind
                   |
                   +-- Kubernetes cluster
```

This keeps Kubernetes cluster creation explicit and makes it easy to
create and delete practice clusters.

Important:

-   Do NOT enable Docker Desktop's built-in Kubernetes just to use kind.
-   Docker Desktop only needs to be running so that its Docker Engine is
    available.
-   Your Kubernetes cluster is created by kind.

------------------------------------------------------------------------

# 4. Prerequisites

You need:

1.  Windows 10/11
2.  Docker Desktop
3.  kind
4.  kubectl
5.  VS Code (recommended, but not required)

## Official download/documentation links

### Docker Desktop for Windows

Official documentation:

https://docs.docker.com/desktop/setup/install/windows-install/

### kind

Official site:

https://kind.sigs.k8s.io/

Official Quick Start:

https://kind.sigs.k8s.io/docs/user/quick-start/

### kubectl

Official Windows installation guide:

https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/

General Kubernetes tools:

https://kubernetes.io/docs/tasks/tools/

### VS Code

Official site:

https://code.visualstudio.com/

### VS Code Kubernetes extension

https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools

------------------------------------------------------------------------

# 5. Step 1 --- Check Docker

Open PowerShell.

Run:

``` powershell
docker version
```

Purpose:

-   checks whether Docker CLI is installed
-   checks whether Docker Engine is reachable

Docker Desktop must be running.

You can also run:

``` powershell
docker info
```

If Docker is working, these commands should return Docker information
instead of a connection error.

------------------------------------------------------------------------

# 6. Step 2 --- Install kind

## Method A --- Winget

This is the easiest method on Windows:

``` powershell
winget install Kubernetes.kind
```

Official kind documentation lists this Windows installation method.

After installation, close PowerShell and open a new PowerShell window.

Check:

``` powershell
kind version
```

Expected output will contain a version such as:

``` text
kind v0.33.0
```

## Method B --- Direct download

The official kind documentation also provides a Windows PowerShell
download:

``` powershell
curl.exe -Lo kind-windows-amd64.exe https://kind.sigs.k8s.io/dl/v0.33.0/kind-windows-amd64
```

Then move and rename it into a directory in PATH.

For example, using a custom directory:

``` powershell
New-Item -ItemType Directory -Path "C:\Tools" -Force
Move-Item ".\kind-windows-amd64.exe" "C:\Tools\kind.exe"
```

Add the directory to your user PATH:

``` powershell
$oldPath = [Environment]::GetEnvironmentVariable("Path", "User")
[Environment]::SetEnvironmentVariable("Path", "$oldPath;C:\Tools", "User")
```

Close PowerShell and open a new one.

Then:

``` powershell
kind version
```

Check where Windows finds it:

``` powershell
where.exe kind
```

Expected:

``` text
C:\Tools\kind.exe
```

### Common mistake

The official example may contain:

``` text
c:\some-dir-in-your-PATH\kind.exe
```

That is a placeholder.

Do NOT literally use `some-dir-in-your-PATH` unless you created that
directory.

------------------------------------------------------------------------

# 7. Step 3 --- Install kubectl

Using winget:

``` powershell
winget install -e --id Kubernetes.kubectl
```

Close PowerShell and open a new one.

Check:

``` powershell
kubectl version --client
```

Also:

``` powershell
where.exe kubectl
```

### kubectl version compatibility

Kubernetes documentation recommends using a kubectl version within one
minor version of the cluster.

For example:

``` text
Cluster v1.37
kubectl v1.36 / v1.37 / v1.38
```

For a kind cluster, check the Kubernetes version supported by your kind
release/node image if you need strict version matching.

------------------------------------------------------------------------

# 8. Step 4 --- Do You Need a Special Folder to Create the Cluster?

No.

You can run:

``` powershell
kind create cluster --name k8s-learning
```

from any directory.

For example:

``` text
C:\Users\YourName>
E:\Downloads>
C:\Projects>
```

The cluster is NOT created inside your current folder.

Your folder is for your project files/YAML.

The actual kind node is a Docker container managed by Docker.

------------------------------------------------------------------------

# 9. Step 5 --- Create the Kubernetes Cluster

Make sure Docker Desktop is running.

Run:

``` powershell
kind create cluster --name k8s-learning
```

What happens:

1.  kind creates a Kubernetes node container.
2.  It bootstraps Kubernetes inside that node.
3.  It starts the Kubernetes control plane.
4.  It configures a kubectl context.
5.  It prepares networking and a default StorageClass.

You can ask kind to wait for readiness:

``` powershell
kind create cluster --name k8s-learning --wait 5m
```

------------------------------------------------------------------------

# 10. Step 6 --- Verify the Cluster

List kind clusters:

``` powershell
kind get clusters
```

Expected:

``` text
k8s-learning
```

Check Docker containers:

``` powershell
docker ps
```

You should see a container similar to:

``` text
k8s-learning-control-plane
```

Check Kubernetes nodes:

``` powershell
kubectl get nodes
```

Expected:

``` text
NAME                         STATUS   ROLES           AGE
k8s-learning-control-plane   Ready    control-plane   ...
```

------------------------------------------------------------------------

# 11. Understanding the kubectl Context

Run:

``` powershell
kubectl config get-contexts
```

You should see a context similar to:

``` text
kind-k8s-learning
```

Check the current context:

``` powershell
kubectl config current-context
```

You can explicitly choose the kind cluster:

``` powershell
kubectl config use-context kind-k8s-learning
```

------------------------------------------------------------------------

# 12. What Does cluster-info Do?

Run:

``` powershell
kubectl cluster-info --context kind-k8s-learning
```

You may see:

``` text
Kubernetes control plane is running at https://127.0.0.1:<port>
CoreDNS is running at https://127.0.0.1:<port>/...
```

Meaning:

-   `kubectl` successfully contacted your cluster.
-   The Kubernetes control plane/API server is reachable.
-   CoreDNS is running.
-   `127.0.0.1` means localhost (your own computer).
-   The number after `:` is a local port.

Do not memorize the port number; it can vary.

------------------------------------------------------------------------

# 13. What Is the Control Plane?

The control plane is the management/decision-making part of Kubernetes.

Main components:

``` text
Control Plane
 |
 +-- kube-apiserver
 |     Kubernetes API/front door
 |
 +-- etcd
 |     Stores cluster state
 |
 +-- kube-scheduler
 |     Chooses nodes for unscheduled Pods
 |
 +-- kube-controller-manager
       Reconciles desired state with actual state
```

A simplified request flow:

``` text
You
 |
kubectl
 |
v
API Server
 |
+----> etcd
 |
+----> controllers
 |
+----> scheduler
 |
v
Node
 |
kubelet
 |
Container runtime
 |
Pod
```

------------------------------------------------------------------------

# 14. What Is CoreDNS?

CoreDNS provides DNS-based service discovery inside Kubernetes.

Suppose:

``` text
Frontend Pod
     |
     | "Where is backend?"
     v
   CoreDNS
     |
     v
Backend Service
```

Instead of hard-coding a changing Pod IP, applications can use
Kubernetes Service DNS names.

This becomes important when learning Services.

------------------------------------------------------------------------

# 15. Where Is the Cluster Data Stored?

With kind, the Kubernetes node is a Docker container.

Run:

``` powershell
docker ps
```

You will see the kind node container.

Your project YAML files are separate:

``` text
kubernetes-practice/
 |
 +-- pod.yaml
 +-- deployment.yaml
 +-- service.yaml
```

Conceptually:

``` text
Windows
 |
 +-- Your project files
 |     +-- YAML
 |
 +-- Docker Desktop
       |
       +-- Docker storage
             |
             +-- kind node container
                    |
                    +-- Kubernetes
```

Do not manually edit Docker's internal storage to manage Kubernetes
resources.

Keep your YAML files in Git instead.

------------------------------------------------------------------------

# 16. Why YAML Is Important

YAML lets you describe the desired state of a Kubernetes resource.

Example:

``` yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-pod

spec:
  containers:
    - name: nginx
      image: nginx
      ports:
        - containerPort: 80
```

Then:

``` powershell
kubectl apply -f pod.yaml
```

Meaning:

> "Kubernetes, make the cluster match what is described in this file."

Important fields:

### apiVersion

Specifies the Kubernetes API version for the resource.

### kind

Specifies the resource type:

``` yaml
kind: Pod
```

or:

``` yaml
kind: Deployment
```

or:

``` yaml
kind: Service
```

### metadata

Identifies the object.

Example:

``` yaml
metadata:
  name: nginx-pod
```

### spec

Describes the desired configuration.

------------------------------------------------------------------------

# 17. Important: kind vs kind

There are TWO meanings of `kind`.

## `kind:` in YAML

``` yaml
kind: Deployment
```

This means the resource type is a Deployment.

## `kind` command

``` powershell
kind create cluster
```

This means the local Kubernetes tool named kind.

They are unrelated uses of the same word.

------------------------------------------------------------------------

# 18. First Kubernetes Practice --- Pod

Create a folder:

``` powershell
mkdir "$HOME\kubernetes-practice"
cd "$HOME\kubernetes-practice"
```

Create:

``` text
pod.yaml
```

Put this in it:

``` yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-pod

spec:
  containers:
    - name: nginx-container
      image: nginx
      ports:
        - containerPort: 80
```

Apply:

``` powershell
kubectl apply -f pod.yaml
```

Check:

``` powershell
kubectl get pods
```

Detailed information:

``` powershell
kubectl describe pod nginx-pod
```

Logs:

``` powershell
kubectl logs nginx-pod
```

Delete:

``` powershell
kubectl delete pod nginx-pod
```

------------------------------------------------------------------------

# 19. Why a Pod Is Not Enough

If you create a Pod directly and delete it:

``` text
Pod
 |
X deleted
```

Kubernetes does not have a Deployment saying:

> "I always want one copy of this application."

For that, use a Deployment.

------------------------------------------------------------------------

# 20. First Deployment

Create:

``` text
deployment.yaml
```

``` yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx-deployment

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
```

Apply:

``` powershell
kubectl apply -f deployment.yaml
```

Check:

``` powershell
kubectl get deployments
```

Check Pods:

``` powershell
kubectl get pods
```

You should have approximately:

``` text
3 nginx Pods
```

------------------------------------------------------------------------

# 21. Scaling

Scale from 3 to 5:

``` powershell
kubectl scale deployment nginx-deployment --replicas=5
```

Check:

``` powershell
kubectl get pods
```

Then scale down:

``` powershell
kubectl scale deployment nginx-deployment --replicas=2
```

This demonstrates the desired-state model.

``` text
Desired = 5
Actual  = 3

Kubernetes creates more Pods

Desired = 5
Actual  = 5
```

------------------------------------------------------------------------

# 22. Self-Healing

Delete one Deployment-managed Pod:

``` powershell
kubectl delete pod <pod-name>
```

Then:

``` powershell
kubectl get pods
```

A replacement Pod should be created because the Deployment wants the
configured number of replicas.

This demonstrates Kubernetes reconciliation/self-healing.

------------------------------------------------------------------------

# 23. Useful Commands Cheat Sheet

## Cluster

``` powershell
kind get clusters
kind create cluster --name k8s-learning
kind delete cluster --name k8s-learning
```

## kubectl context

``` powershell
kubectl config get-contexts
kubectl config current-context
kubectl config use-context kind-k8s-learning
```

## Cluster information

``` powershell
kubectl cluster-info
kubectl cluster-info --context kind-k8s-learning
kubectl get nodes
```

## Pods

``` powershell
kubectl get pods
kubectl get pods -A
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl delete pod <pod-name>
```

## Deployments

``` powershell
kubectl get deployments
kubectl describe deployment <deployment-name>
kubectl scale deployment <deployment-name> --replicas=5
kubectl delete deployment <deployment-name>
```

## YAML

``` powershell
kubectl apply -f file.yaml
kubectl delete -f file.yaml
```

## Docker

``` powershell
docker ps
docker images
docker info
docker version
```

------------------------------------------------------------------------

# 24. Loading Your Own Docker Image into kind

Suppose you build:

``` powershell
docker build -t my-app:1.0 .
```

The image exists in Docker, but Kubernetes kind nodes may not
automatically have that image.

Load it:

``` powershell
kind load docker-image my-app:1.0 --name k8s-learning
```

Then reference it in your Kubernetes YAML:

``` yaml
containers:
  - name: my-app
    image: my-app:1.0
```

For locally loaded images, avoid relying on the implicit `:latest`
behavior; use an explicit tag and an appropriate image pull policy when
needed.

------------------------------------------------------------------------

# 25. VS Code Setup

Install VS Code:

https://code.visualstudio.com/

Install the Kubernetes extension:

https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools

The extension can help you:

-   browse clusters
-   inspect Pods
-   inspect Deployments
-   inspect Services
-   edit Kubernetes manifests
-   view logs
-   run commands in Pods

You should still learn the `kubectl` commands because understanding the
CLI is important.

------------------------------------------------------------------------

# 26. Recommended Project Structure

Use this structure for your learning repository:

``` text
kubernetes-local-windows-guide/
|
+-- README.md
|
+-- docs/
|   +-- 01-concepts.md
|   +-- 02-installation.md
|   +-- 03-kind-cluster.md
|   +-- 04-kubectl.md
|   +-- 05-yaml.md
|   +-- 06-troubleshooting.md
|
+-- manifests/
|   +-- pod/
|   |    +-- nginx-pod.yaml
|   |
|   +-- deployment/
|        +-- nginx-deployment.yaml
|
+-- commands/
|   +-- kubectl-cheatsheet.md
|
+-- .gitignore
```

------------------------------------------------------------------------

# 27. Troubleshooting

## `kind` is not recognized

Check:

``` powershell
where.exe kind
```

If nothing is returned, check that `kind.exe` exists in a directory on
PATH.

Example:

``` powershell
Get-Item "C:\Tools\kind.exe"
```

If needed, add `C:\Tools` to your user PATH and restart PowerShell.

------------------------------------------------------------------------

## `kubectl` is not recognized

Install:

``` powershell
winget install -e --id Kubernetes.kubectl
```

Then restart PowerShell.

Check:

``` powershell
where.exe kubectl
kubectl version --client
```

------------------------------------------------------------------------

## Docker is not running

Check:

``` powershell
docker version
```

Start Docker Desktop and try again.

------------------------------------------------------------------------

## kind cannot create the cluster

Check:

``` powershell
docker info
kind version
kubectl version --client
```

Then try:

``` powershell
kind create cluster --name k8s-learning
```

For more details:

``` powershell
kind create cluster --name k8s-learning --verbosity 5
```

------------------------------------------------------------------------

## The wrong Kubernetes cluster is selected

Check:

``` powershell
kubectl config get-contexts
```

Select:

``` powershell
kubectl config use-context kind-k8s-learning
```

Verify:

``` powershell
kubectl config current-context
```

------------------------------------------------------------------------

## Cluster disappeared

Check:

``` powershell
kind get clusters
```

If the cluster is absent, recreate it:

``` powershell
kind create cluster --name k8s-learning
```

Your YAML files are separate from the cluster, so you can recreate
resources:

``` powershell
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

This is one reason to keep Kubernetes manifests in Git.

------------------------------------------------------------------------

# 28. Stop vs Delete

Be careful with these concepts.

### Delete the kind cluster

``` powershell
kind delete cluster --name k8s-learning
```

This removes the kind cluster/node containers.

### Create it again

``` powershell
kind create cluster --name k8s-learning
```

Your Git/YAML files are unaffected.

The YAML files describe what you want; the cluster is the runtime
environment.

------------------------------------------------------------------------

# 29. Learning Roadmap

After setup, learn in this order:

``` text
1. Kubernetes architecture
        |
2. kubectl
        |
3. YAML
        |
4. Pods
        |
5. Deployments
        |
6. ReplicaSets
        |
7. Services
        |
8. Namespaces
        |
9. ConfigMaps
        |
10. Secrets
        |
11. Volumes
        |
12. Probes
        |
13. Resource requests/limits
        |
14. Rolling updates
        |
15. Rollbacks
        |
16. Ingress
        |
17. Networking
        |
18. Jobs / CronJobs
        |
19. StatefulSets
        |
20. Helm
        |
21. Multi-node clusters
        |
22. Kubernetes troubleshooting
```

------------------------------------------------------------------------

# 30. Golden Rules for Beginners

1.  **Docker and Kubernetes are not the same thing.**
2.  Docker runs/packages containers; Kubernetes orchestrates workloads.
3.  A cluster is the whole Kubernetes environment.
4.  A node is a machine/environment in the cluster.
5.  A Pod is the smallest deployable Kubernetes unit.
6.  `kubectl` is the command-line client.
7.  `kind` creates local Kubernetes clusters using Docker containers as
    nodes.
8.  `kind:` in YAML is different from the `kind` CLI tool.
9.  YAML describes desired state.
10. Keep your YAML/manifests in Git.
11. Do not manually modify Docker's internal storage.
12. Learn `kubectl`, even if you use the VS Code Kubernetes extension.
13. For local learning, you do not need a cloud Kubernetes service.
14. Start with one-node clusters; learn multi-node clusters later.

------------------------------------------------------------------------

# 31. Quick Start After Everything Is Installed

If Docker Desktop is running and kind/kubectl are installed:

``` powershell
kind create cluster --name k8s-learning
kubectl config use-context kind-k8s-learning
kubectl cluster-info --context kind-k8s-learning
kubectl get nodes
kubectl get pods -A
```

Then create a practice folder:

``` powershell
mkdir "$HOME\kubernetes-practice"
cd "$HOME\kubernetes-practice"
```

Create a YAML manifest and apply it:

``` powershell
kubectl apply -f deployment.yaml
```

Check:

``` powershell
kubectl get deployments
kubectl get pods
```

------------------------------------------------------------------------

# 32. Official References

-   kind: https://kind.sigs.k8s.io/
-   kind Quick Start: https://kind.sigs.k8s.io/docs/user/quick-start/
-   Kubernetes documentation: https://kubernetes.io/docs/
-   kubectl Windows installation:
    https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/
-   Kubernetes tools: https://kubernetes.io/docs/tasks/tools/
-   Docker Desktop Windows installation:
    https://docs.docker.com/desktop/setup/install/windows-install/
-   Docker Desktop documentation: https://docs.docker.com/desktop/
-   VS Code: https://code.visualstudio.com/
-   VS Code Kubernetes extension:
    https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools

------------------------------------------------------------------------

## Final Mental Model

``` text
                         YOU
                          |
                       VS Code
                          |
                       kubectl
                          |
                 Kubernetes API Server
                          |
              +-----------+-----------+
              |                       |
          Control Plane             Node
                                      |
                                     Pod
                                      |
                                  Container
                                      |
                                  Application
```

And for this local setup:

``` text
Windows
  |
Docker Desktop
  |
Docker Engine
  |
kind
  |
Kubernetes cluster
  |
kubectl
  |
Your Kubernetes workloads
```

If you understand this diagram, you already understand the foundation of
the environment you are building.
