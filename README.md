# Kubernetes summary

Yiadh TLIJANI, April 2019, (for K8s 1,3)

# 1. The CKAD Curriculum

The online, proctored, performance-based test consists of a set of performance-based items (problems) to be solved in a command line and is expected to take approximately two (2) hours to complete.

This exam curriculum includes these general domains and their weights on the exam:

* **13% – Core Concepts:** This covers the API primitives and how to create and configure basic Pods
* **18% - Configuration:** This involves understanding ConfigMaps, SecurityContexts, defining an applications resource requirements, creating and consuming secrets and understanding ServiceAccounts
* **10% - Multi-Container Pods:** This tests your knowledge of the multi-container design patterns such as ambassador, adapter, and sidecar)
* **18% – Observability:** This is for understanding Liveness and Readiness Probes, understanding container logging, as well as how to monitor applications in K8s and understanding debugging.
* **20% – Pod Design:** This is things like how to use Labels, Selectors, and Annotations as well as how deployments work and how to perform rollbacks. Finally, it covers Jobs and CronJobs.
* **13% – Services & Networking:** This section involves understanding services and demonstrating a basic understanding of NetworkPolicies
* **08% – State Persistence:** This requires knowledge of PersistentVolumeClaims for storage



# 2. Core Concepts

## 2.1 Kubernetes architecture

### 2.1.1 Control Plane

**Services on Master Nodes:** entities responsible for managing cluster state.

| Name                     | Summary |
| ------------------------ | ------------- |
| kube-apiserver           | exposes the Kubernetes API from master nodes  |
| etcd                     | reliable data store for all k8s cluster data  |
| kube-scheduler           | schedule pods to run on selected nodes |
| kube-controller-manager  | node controller, replication controller, endpoints controller, and service account & token controllers |


**Services on Worker Nodes:** individual machines or VMs that make up the cluster

| Name              | Summary                                                                                   |
| ----------------- | ----------------------------------------------------------------------------------------- |
| kubelet           | makes sure that containers are running in a pod                                           |
| kube-proxy        | perform connection forwarding                                                             |
| Container Runtime | Kubernetes supported runtimes: Docker, rkt, runc and any OCI runtime-spec implementation. |

### 2.1.2 Extra components

**Addons:** - pods and services that implement cluster features

| Name                          | Summary                                                                   |
| ----------------------------- | ------------------------------------------------------------------------- |
| DNS                           | serves DNS records for Kubernetes services                                |
| Web UI                        | a general purpose, web-based UI for Kubernetes clusters                   |
| Container Resource Monitoring | collect, store and serve container metrics                                |
| Cluster-level Logging         | save container logs to a central log store with search/browsing interface |

**Tools:**

| Name                  | Summary                                                     |
| --------------------- | ----------------------------------------------------------- |
| kubectl               | the command line util to talk to k8s cluster                |
| kubeadm               | the command to bootstrap the cluster                        |
| kubefed               | the command line to control a Kubernetes Cluster Federation |

**More Resources**

https://kubernetes.io/docs/reference/kubectl/cheatsheet/

https://codefresh.io/kubernetes-guides/kubernetes-cheat-sheet/

https://kubernetes.io/docs/concepts/overview/components/



## 2.2 Kubernetes basic objects

### 2.2.1 Basic units

**pod** = container / set of containers + storage resources + unique IP + local options

**service** = abstraction layer on top of a set of ephemeral pods (think of this as the ‘face’ of a set of pods)

**volume** = sometimes-shared, persistent storage

**namespace** = virtual cluster on top of an underlying physical cluster

### 2.2.2 Service Types

**clusterIP** = exposes services only inside the cluster (default)

**nodePort** = exposes services at the specified port on all nodes (<node-ip>:<nodePort>)

**loadBalancer** = exposes the service with a cloud-provider’s load balancer.

**externalName** = this maps a service to endpoints completely outside of the cluster


### 2,2,3 Controllers

**replicationcontroller** = ensures a certain number of pods are running

**replicaSet** = declared in essentially the same way as Replication Controllers, except that they have more options for the selector.

**deployment** = declaratively manages a replicaSet

**statefulSet** = like a deployment, but for non-interchangeable (or stateful) underlying pods

**daemonSet** = manages pods that need to run on all/some nodes

**job** = manages a set of pods that run to completion and tracks the overall progress

**Links:**

https://www.mirantis.com/blog/kubernetes-replication-controller-replica-set-and-deployments-understanding-replication-options/

### 2.3.4 RBAC Authorization objects

In the RBAC API, a role contains rules that represent a set of permissions. Permissions are purely additive (there are no “deny” rules). A role can be defined within a namespace with a Role, or cluster-wide with a ClusterRole.

**Role** = used to grant access to resources within a single namespace

**ClusterRole** = can be used to grant the same permissions as a Role, but because they are cluster-scoped. They can also be used to grant access to:
- cluster-scoped resources (like nodes)
- non-resource endpoints (like “/healthz”)
- namespaced resources (like pods) across all namespaces (needed to run kubectl get pods --all-namespaces, for example)

**RoleBinding** = grants the permissions defined in a role to a user or set of users

A RoleBinding may also reference a ClusterRole to grant the permissions to namespaced resources defined in the ClusterRole within the RoleBinding’s namespace. This allows administrators to define a set of common roles for the entire cluster, then reuse them within multiple namespaces.

Example: Create a ClusterRole called pod-reader, and use it to grant permissions to read pods within the RoleBinding’s namespace.

**ClusterRoleBinding** =  grants the permissions defined in a ClusterRole to a user or set of users

### 2.3.5 Security and policy objects

**NetworkPolicy** = a specification of how groups of pods are allowed to communicate with each other and other network endpoints.

By default, pods are non-isolated; all pods can reach each other; all ingress and egress traffic is allowed.

Pods become isolated by having a NetworkPolicy that selects them. Once there is any NetworkPolicy in a namespace selecting a particular pod, that pod will reject any connections that are not allowed by any NetworkPolicy. (Other pods in the namespace that are not selected by any NetworkPolicy will continue to accept all traffic.)

**Security Context**

Pods and containers within pods can be given specific security constraints to limit what processes running in containers can do. For expale UID of the process, the Linux capabilities, and the filesystem group can be limited.
