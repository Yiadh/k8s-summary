# Kubernetes summary

Yiadh TLIJANI, April 2019, (for K8s 1,3)

## The CKAD Curriculum

The online, proctored, performance-based test consists of a set of performance-based items (problems) to be solved in a command line and is expected to take approximately two (2) hours to complete.

This exam curriculum includes these general domains and their weights on the exam:

* **13% – Core Concepts:** This covers the API primitives and how to create and configure basic Pods
* **18% - Configuration:** This involves understanding ConfigMaps, SecurityContexts, defining an applications resource requirements, creating and consuming secrets and understanding ServiceAccounts
* **10% - Multi-Container Pods:** This tests your knowledge of the multi-container design patterns such as ambassador, adapter, and sidecar)
* **18% – Observability:** This is for understanding Liveness and Readiness Probes, understanding container logging, as well as how to monitor applications in K8s and understanding debugging.
* **20% – Pod Design:** This is things like how to use Labels, Selectors, and Annotations as well as how deployments work and how to perform rollbacks. Finally, it covers Jobs and CronJobs.
* **13% – Services & Networking:** This section involves understanding services and demonstrating a basic understanding of NetworkPolicies
* **08% – State Persistence:** This requires knowledge of PersistentVolumeClaims for storage

## Definitions

Let’s check out some terminology below:

### Basic Objects

**pod** = container / set of containers + storage resources + unique IP + local options

**service** = abstraction layer on top of a set of ephemeral pods (think of this as the ‘face’ of a set of pods)

**volume** = sometimes-shared, persistent storage

**namespace** = virtual cluster on top of an underlying physical cluster

## Service Types

**clusterIP** = exposes services only inside the cluster (default)

**nodePort** = exposes services at the specified port on all nodes (<node-ip>:<nodePort>)

**loadBalancer** = exposes the service with a cloud-provider’s load balancer.

**externalName** = this maps a service to endpoints completely outside of the cluster

## Controllers

**replicaSet** = ensures a certain number of pods are running

**deployment** = declaratively manages a replicaSet

**statefulSet** = like a deployment, but for non-interchangeable (or stateful) underlying pods

**daemonSet** = manages pods that need to run on all/some nodes

**job** = manages a set of pods that run to completion and tracks the overall progress

## Components & Services

### Control Plane

| First Header  | Second Header |
| ------------- | ------------- |
| Content Cell  | Content Cell  |
| Content Cell  | Content Cell  |

| Name                         | Summary                    |
|------------------------------+----------------------------|
| List api group               | =kubectl api-versions=     |
| List all CRD                 | =kubectl get crd=          |
| List storageclass            | =kubectl get storageclass= |
| List all supported resources | =kubectl api-resources=    |


*** Services on Master Nodes: entities responsible for managing cluster state.
| Name                    | Summary                                                                                                |
|-------------------------+--------------------------------------------------------------------------------------------------------|
| [[https://github.com/kubernetes/kubernetes/tree/master/cmd/kube-apiserver][kube-apiserver]]          | exposes the Kubernetes API from master nodes                                                           |
| [[https://coreos.com/etcd/][etcd]]                    | reliable data store for all k8s cluster data                                                           |
| [[https://github.com/kubernetes/kubernetes/tree/master/cmd/kube-scheduler][kube-scheduler]]          | schedule pods to run on selected nodes                                                                 |
| [[https://github.com/kubernetes/kubernetes/tree/master/cmd/kube-controller-manager][kube-controller-manager]] | node controller, replication controller, endpoints controller, and service account & token controllers |

*** Services on Worker Nodes: individual machines or VMs that make up the cluster
| Name              | Summary                                                                                   |
|-------------------+-------------------------------------------------------------------------------------------|
| [[https://github.com/kubernetes/kubernetes/tree/master/cmd/kubelet][kubelet]]           | makes sure that containers are running in a pod                                           |
| [[https://github.com/kubernetes/kubernetes/tree/master/cmd/kube-proxy][kube-proxy]]        | perform connection forwarding                                                             |
| [[https://github.com/docker/engine][Container Runtime]] | Kubernetes supported runtimes: Docker, rkt, runc and any [[https://github.com/opencontainers/runtime-spec][OCI runtime-spec]] implementation. |

*** Addons: pods and services that implement cluster features
| Name                          | Summary                                                                   |
|-------------------------------+---------------------------------------------------------------------------|
| DNS                           | serves DNS records for Kubernetes services                                |
| Web UI                        | a general purpose, web-based UI for Kubernetes clusters                   |
| Container Resource Monitoring | collect, store and serve container metrics                                |
| Cluster-level Logging         | save container logs to a central log store with search/browsing interface |

*** Tools
| Name                  | Summary                                                     |
|-----------------------+-------------------------------------------------------------|
| [[https://github.com/kubernetes/kubernetes/tree/master/cmd/kubectl][kubectl]]               | the command line util to talk to k8s cluster                |
| [[https://github.com/kubernetes/kubernetes/tree/master/cmd/kubeadm][kubeadm]]               | the command to bootstrap the cluster                        |
| [[https://kubernetes.io/docs/reference/setup-tools/kubefed/kubefed/][kubefed]]               | the command line to control a Kubernetes Cluster Federation |
| Kubernetes Components | [[https://kubernetes.io/docs/concepts/overview/components/][Link: Kubernetes Components]]                                 |

** More Resources
License: Code is licensed under [[https://www.dennyzhang.com/wp-content/mit_license.txt][MIT License]].

https://kubernetes.io/docs/reference/kubectl/cheatsheet/

https://codefresh.io/kubernetes-guides/kubernetes-cheat-sheet/







## Core Concepts


