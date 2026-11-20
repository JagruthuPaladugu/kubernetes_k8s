# Kubernetes Practicals

Hands-on Kubernetes practice using **Docker Desktop**, **KIND**, and **kubectl** on Windows.

## Experiments

1. [Experiment 1 – Kubernetes Basics](Experiment-1-Kubernetes-Basics/commands.md)
   - Created a 3-node KIND cluster.
   - Created and inspected an Nginx Pod.
   - Used port-forwarding and deleted the Pod.

2. [Experiment 2 – Trace `kubectl create deployment` End to End](Experiment-2-Deployment-End-to-End/commands.md)
   - Created a Deployment and observed Pods and ReplicaSets.
   - Verified self-healing and scaling.
   - Inspected etcd, container runtime, kubelet logs, Services, and EndpointSlices.
   - Tested optimistic concurrency and cleaned up the workload.

## Environment

- Windows CMD
- Docker Desktop
- KIND
- kubectl
- Kubernetes v1.37.0 (KIND nodes)

## Repository structure

```text
kubernetes-practicals/
├── README.md
├── Experiment-1-Kubernetes-Basics/
│   ├── commands.md
│   └── screenshots/
└── Experiment-2-Deployment-End-to-End/
    ├── commands.md
    └── screenshots/
```

## Notes

- Add your own terminal/browser screenshots to each `screenshots/` folder.
- Do not upload Docker images or running containers; document the commands and results instead.
- Do not commit generated files containing credentials, tokens, or private information.
- `web-old.yaml` was created for the stale `resourceVersion` conflict test. It is a temporary test file and is intentionally not included.
