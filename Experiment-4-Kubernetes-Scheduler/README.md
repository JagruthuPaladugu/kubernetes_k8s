\# Experiment 4 – Kubernetes Scheduler



\## Objective



To understand Kubernetes scheduling architecture, scheduling cycles, filtering, scoring, and scheduler plugins through practical experiments.



\## Environment



\- Kubernetes v1.37.0

\- Docker Desktop

\- KIND Cluster

\- One control-plane node

\- Two worker nodes

\- Windows PowerShell



\## Scheduler Plugins Explored



| Plugin | Purpose |

|---|---|

| NodeResourcesFit | Checks available CPU and memory resources |

| NodeAffinity | Selects nodes based on labels |

| TaintToleration | Controls scheduling on tainted nodes |

| NodePorts | Checks host port availability |

| NodeUnschedulable | Prevents scheduling on cordoned nodes |



\## Files



| File | Description |

|---|---|

| big.yaml | Demonstrates insufficient CPU resources |

| ssd.yaml | Demonstrates node selection using SSD labels |

| spread-tol.yaml | Demonstrates taints and tolerations |

| hostport.yaml | Demonstrates host port conflicts |



\## Practical Activities



\- Kubernetes scheduler source code analysis

\- Default scheduler plugins and weights

\- Filtering and scoring mechanisms

\- Scheduling and binding cycles

\- Node labeling

\- Taints and tolerations

\- Host port conflict detection

\- Node cordoning and uncordoning

\- Resource cleanup



\## Result



Successfully demonstrated Kubernetes scheduling mechanisms and verified scheduler behavior through practical experiments. All experiment resources were cleaned up, and all three cluster nodes were verified to be in the Ready state.



\## Status



Successfully Completed.

