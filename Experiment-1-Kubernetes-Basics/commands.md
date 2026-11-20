# Experiment 1 – Kubernetes Basics

## Aim

Create a local multi-node Kubernetes cluster with KIND, deploy an Nginx Pod, inspect it, access it using port-forwarding, and delete it.

## Environment

- Windows CMD
- Docker Desktop
- KIND
- kubectl
- Cluster name: `klu`
- Nodes: `klu-control-plane`, `klu-worker`, `klu-worker2`

## Procedure and commands

### 1. Create the KIND cluster

```cmd
kind create cluster --name klu --config kind-3node.yaml
```

### 2. Check the cluster nodes

```cmd
kubectl get nodes
kubectl cluster-info
```

Expected: the control-plane and two worker nodes become `Ready`.

### 3. Create an Nginx Pod

```cmd
kubectl run nginx --image=nginx
```

### 4. Check the Pod

```cmd
kubectl get pods
kubectl get pods -o wide
kubectl describe pod nginx
```

Expected: the Nginx Pod reaches `Running`.

### 5. Access Nginx using port-forwarding

```cmd
kubectl port-forward pod/nginx 8080:80
```

Keep this command running. Open `http://localhost:8080` in a browser and verify the Nginx welcome page.

Press `Ctrl+C` in CMD to stop port-forwarding.

### 6. Delete the test Pod

```cmd
kubectl delete pod nginx
kubectl get pods
```

## Result

A 3-node KIND cluster was created successfully. An Nginx Pod was deployed, inspected, accessed through port-forwarding, and deleted.

## Evidence to add

Save terminal and browser screenshots in this folder's `screenshots/` directory.
