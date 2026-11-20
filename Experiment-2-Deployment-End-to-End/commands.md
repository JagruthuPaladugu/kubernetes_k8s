# Experiment 2 – Trace `kubectl create deployment` End to End

## Aim

Trace a Kubernetes Deployment from the `kubectl` command through ReplicaSet and Pod creation, node scheduling, container startup, Service discovery, and API object storage.

## Environment

- Windows CMD (unless stated otherwise)
- Docker Desktop
- KIND
- kubectl
- Cluster/context: `klu` / `kind-klu`
- Kubernetes version: v1.37.0
- Worker nodes: `klu-worker`, `klu-worker2`

## 1. Select context and check nodes

```cmd
kubectl config use-context kind-klu
kubectl get nodes
```

Expected: all three KIND nodes are `Ready`.

## 2. Create a Deployment

```cmd
kubectl create deployment web --image=nginx:1.27 --replicas=2 -v=6
kubectl get deployments
kubectl get pods -o wide
```

Observed: Deployment `web` reached `2/2` Ready, with two Nginx Pods running on the worker nodes.

Optional: to inspect API request details in **PowerShell**, use:

```powershell
kubectl create deployment web --image=nginx:1.27 --replicas=2 -v=6 2>&1 | Select-String "deployments"
```

Do not rerun the create command if the Deployment already exists. The command above is only an example for a fresh test.

## 3. Inspect the Deployment → ReplicaSet ownership

```cmd
kubectl get rs -l app=web -o "custom-columns=RS:.metadata.name,OWNER:.metadata.ownerReferences[0].name"
kubectl get pods -o wide
```

Observed owner chain: ReplicaSet `web-69c6f74b8b` was owned by Deployment `web`. The two Pods were `Running` on `klu-worker` and `klu-worker2`.

## 4. Inspect events

```cmd
kubectl get events --sort-by=.metadata.creationTimestamp
kubectl get events -w
```

Events can include older records from earlier experiments. Identify the timestamps and names for the current Deployment when interpreting the output.

## 5. Verify API objects in etcd

Run in Windows CMD:

```cmd
kubectl -n kube-system exec etcd-klu-control-plane -- etcdctl --endpoints=https://127.0.0.1:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key get /registry --prefix --keys-only
```

Relevant keys observed:

```text
/registry/deployments/default/web
/registry/replicasets/default/web-69c6f74b8b
/registry/pods/default/web-69c6f74b8b-b7v76
/registry/pods/default/web-69c6f74b8b-xftgk
```

This verified that the Deployment, ReplicaSet, and Pod API objects were stored in etcd.

## 6. Inspect containers using the container runtime

```cmd
docker exec klu-worker crictl ps
docker exec klu-worker2 crictl ps
```

Observed: each worker had a running `nginx` container associated with its respective `web` Pod. The container runtime was containerd.

## 7. Inspect kubelet logs

```cmd
docker exec klu-worker journalctl -u kubelet --no-pager -n 30
```

Observed kubelet log evidence included Pod update notifications (`ADDED`) and an `Observed pod startup duration` entry for the `web` Pod on `klu-worker`.

The worker also registered successfully with the cluster. Some initial startup log errors were transient; the final Pod state was `Running`.

## 8. Create a Service and inspect EndpointSlices

```cmd
kubectl expose deployment web --port=80
kubectl get service web
kubectl get endpointslices -l kubernetes.io/service-name=web
```

Observed Service:

- Type: `ClusterIP`
- Cluster IP: `10.96.229.40`
- Port: `80/TCP`

Observed EndpointSlice addresses:

```text
10.244.1.5
10.244.2.6
```

These were the two Pod IPs registered as endpoints for the Service.

Optional kube-proxy rule inspection:

```cmd
docker exec klu-worker iptables-save | findstr "KUBE-SVC"
```

Depending on the cluster's networking implementation, this filter may not display matching rules.

## 9. Test optimistic concurrency

Save the current Deployment YAML:

```cmd
kubectl get deployment web -o yaml > web-old.yaml
findstr "resourceVersion" web-old.yaml
```

Observed old resource version: `4140`.

Update the Deployment:

```cmd
kubectl scale deployment web --replicas=3
```

Try replacing it with the stale YAML:

```cmd
kubectl replace -f web-old.yaml
```

Observed result:

```text
Error from server (Conflict): ... the object has been modified;
please apply your changes to the latest version and try again
```

This is the expected optimistic concurrency behavior: Kubernetes rejected an update based on an outdated `resourceVersion` (HTTP 409 Conflict).

## 10. Cleanup

```cmd
kubectl delete service web
kubectl delete deployment web
kubectl get deployments,pods,services
```

Observed: the `web` Service, Deployment, and Pods were removed. The built-in `kubernetes` Service remained, which is normal.

## Result

The Deployment lifecycle was traced end to end. The practical verified Deployment/ReplicaSet/Pod ownership, etcd object keys, running containers, kubelet startup logs, Service endpoints, and optimistic concurrency conflict. The workload was cleaned up while the KIND cluster was kept for future practicals.

## Evidence to add

Save terminal screenshots in this folder's `screenshots/` directory. Hide any sensitive local information before sharing screenshots publicly.
