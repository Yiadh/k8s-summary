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

However there is one major difference between replication controller and replica set. Replica Set requires a selector definition.

The selector section helps the Replica Set identify what PODs fall under it.

But why would you have to specify what PODs fall under it if you have provided the contents of the POD definition file itself in the template. It's because ReplicaSet can also manage PODs that were not created as part of the replica set creation.

So for example there were PODs created before the creation of the replica set that match labels specified in the selector. The Replica set will also take those PODs into consideration when creating the replicas.

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


# 3. Configuration

## 3.1 Commands and Arguments for Docker and Kubernetes

command (resp. args) in k8s is the equivalent for ENTRYPOINT (resp. CMD) in Docker


    docker run --entrypoint sleep ubuntu 10

    command: ["sleep"]
    args:
    - "10"


## 3.2 Environment variables

## 3.3 ConfigMaps and Secrets

They could be mounted as environment variables, single envionment variable or volume.

## 3.4 Security Context

### 3.4.1 Docker containers anatomy

Containers created by Docker, from Docker-formatted container images, are isolated from each other by several standard features of the Linux kernel. These include:

**Namespaces**
The kernel can place specific system resources that are normally visible to all processes into a namespace. Inside a namespace, only processes that are members of that namespace can see those resources. Resources that can be placed into a namespace include network interfaces, the process ID list, mount points, IPC resources, and the system's own hostname information. As an example, two processes in two different mounted namespaces have different views of what the mounted root file system is. Each container is added to a specific set of namespaces, which are only used by that container.

**Control groups (cgroups)**
Control groups partition sets of processes and their children into groups in order to manage and limit the resources they consume. Control groups place restrictions on the amount of system resources the processes belonging to a specific container might use. This keeps one container from using too many resources on the container host.

**SELinux**
SELinux is a mandatory access control system that is used to protect containers from each other and to protect the container host from its own running containers. Standard SELinux type enforcement is used to protect the host system from running containers. Container processes run as a confined SELinux type that has limited access to host system resources. In addition, sVirt uses SELinux Multi-Category Security (MCS) to protect containers from each other. Each container's processes are placed in a unique category to isolate them from each other.


### 3.4.2 Docker security

When you list the processes on the host, you see a list of processes including the process running inside the container [having the id 1] but with a different process ID. This is because the processes can have different process IDs in different namespaces and that's how

Docker isolates containers within the system. So that's process isolation.

By default Docker runs processes within containers as the root user. Both within the container and outside the container on the host the process is run as the root user.

To inforce docker security, you can:

- Now if you do not want the process within the container to run as the root, user you may set the user using the user option within the docker run command and specify the new user ID.

- Another way to enforce user security is to have this defined in the docker image itself at the time of creation. For example, we will use the default ubuntu image and set the user id to 1000 using the user instruction.

- limit the abilities of the root user within the container. 

Docker implements a set of security features that limits the abilities of the root user within the container. So the root user within the container isn't really like the root user on the host. Docker uses Linux capabilities to implement this.

By default docker runs a container with a limited set of capabilities. And so the processes running within the container do not have the privileges to say reboot the host or perform operations that can disrupt the host or other containers running on the same host.

If you wish to override this behavior and provide additional privileges then what is available, use the cap-add or cap-drop or privileged option in the docker run command.

    docker run --cap-add MAC_ADMIN --name ubuntu-ctnr ubuntu

### 3.4.3 Linux capabilities

all linux capabilities are located under /usr/include/linux/capability.h

An easier way to get further information regarding linux capabilities:

    man capabilities

### 3.4.4 Kubernetes security context

Security context could be defined at pod level or container level.

The configuration at the container level overides the pod level.

Capabilities are only supported at the container level not at the pod level.

    security_context:
      runAsUser: 1000
      capabilities:
        add: ["MAC_ADMIN"]

## 3.5 Service Account

The concept of service accounts is linked to other security related concepts and Kubernetes such as authentication, authorization, role-based access controls cetera.

There are two types of accounts in Kubernetes - a user account and a service account.

- User account: As you might already know the user account is used by humans and service accounts are used by machines.

A user account could be for an administrator accessing the cluster to perform administrative tasks or a developer accessing the cluster to deploy applications etc..

- Service account could be an account used by an application to interact with a Kubernetes cluster. It helps applications interracting with the k8s api server.

For example a monitoring application like Prometheus is used as a service account to pull the Kubernetes API for performance metrics. An automated build tool like Jenkin's uses service accounts to deploy applications on the Kubernetes cluster.

The Serviceaccount is defined at pod level

## 3.6 Resource Requirements

By default Kubernetes sets a limit of 1 vCPU to containers. So if you do not specify explicitly a container will be limited to consume only one vCPU from the node.

The same goes with memory. By default Kubernetes has set a limit of 512 Mebibyte on containers.

Remember that the limits and requests are set for each container within the POD. So what happens when a POD tries to exceed resources beyond its specified limit?

In case of CPU, kubernetes throttles the CPU so that it does not go beyond the specified limit. A container cannot use more CPU resources than its limit.

However this is not the case with the memory a container can use more memory resources than its limit. So if a POD tries to consume more memory than its limit constantly the POD will be terminated.

The status 'OOMKilled' indicates that the pod ran out of memory.

If there is no enough resources for a pod to be scheduled, it remains at the status pending.

Syntax:

    resources:
      limits:
        cpu: 1
        memory: 1Gi
      requests:
        cpu: 0.5
        memory: 512Mi

## 3.7 Taints and Tolerations

Used to define the pod to node relationship and how you can restrict what pods are placed on what nodes.

A pod could be scheduled on a specific node if and only if it is tolerant to all the target node taints.

Node Taint syntax:

    kubectl taint nodes nodeName key=value:taintEffect

To remove the taint added by the command above, you can run:

    kubectl taint nodes nodeName key=value:taintEffect-


The taint effect defines what would happen to the pods if they do not tolerate the taint. There are three main effects:

- **NoSchedule** which means the parts will not be Scheduled on the node which is what we have been discussing,

- **PreferNoSchedule** which means the system will try to avoid placing it pod on the node but that is not guaranteed.

- **NoExecute** which means that the new pod will be evicted from the node (if it is already running on the node), and will not be scheduled onto the node (if it is not yet running on the node).


The NoExecute taint effect affects pods that are already running on the node as follows:

- pods that do not tolerate the taint are evicted immediately

- pods that tolerate the taint without specifying tolerationSeconds in their toleration specification remain bound forever

- pods that tolerate the taint with a specified tolerationSeconds remain bound for the specified amount of time


Pod toleration syntax:

    tolerations:
    - key: "key"
      operator: "Equal"
      value: "value"
      effect: "NoSchedule"
      tolerationSeconds: 3600

And remember all of these values need to be encoded in double quotes. 

tolerationSeconds field dictates how long the pod will stay bound to the node after the taint is added.

A toleration “matches” a taint if the keys are the same and the effects are the same, and:

- the operator is Exists (in which case no value should be specified), or

- the operator is Equal and the values are equal

**Notes:**

Operator defaults to Equal if not specified.

An empty key with operator Exists matches all keys, values and effects which means this will tolerate everything.

     tolerations:
    - operator: "Exists"

An empty effect matches all effects with key key.

    tolerations:
    - key: "key"
      operator: "Exists"


So remember taints and tolerations does not tell the pot to go to a particular node. Instead it tells the node to only accept pods with certain tolerations.

If your requirement is to restrict a pod to certain nodes it is achieved through another concept called as node affinity, which we will discuss in the next lecture.

## 3.8 Node Selectors

You can constrain a pod to only be able to run on particular nodes or to prefer to run on particular nodes. There are several ways to do this, and the recommended approaches all use label selectors to make the selection.

nodeSelector is a field of PodSpec. It specifies a map of key-value pairs. For the pod to be eligible to run on a node, the node must have each of the indicated key-value pairs as labels (it can have additional labels as well).

Add label to a specific node

    kubectl label nodes <node-name> <label-key>:<label-value>
    kubectl label nodes node01 size:large

Delete label from node

    kubectl label node <node-name> <label-key>-

Add a constrain to a pod to only be able to run on particular nodes having the mentionned above keyvalue label

    nodeSelector:
      <label-key>: <label-value>

We used a single label and selector to achieve our goal here.

But what if our requirement is much more complex?

For example we would like to say something like :"place the pod on a large or medium node", or something like: "place the pod on any nodes that are not small".

You cannot achieve this using node selectors. For this node infinity and anti-affinity features were introduced.

## 3.9 Node Affinity

Using node selectors you cannot provide advanced expressions like OR or NOT with node selectors. Node Affinity feature provides us with advanced capabilities to limit pod placement on specific nodes.

There are currently two types of node affinity available:

- requiredDuringSchedulingIgnoredDuringExecution

- preferredDuringSchedulingIgnoredDuringExecution.

Check syntax for adding a constrain to a pod to only be able to run on particular nodes

    kubectl explain pod --recursive


## 3.10 Pod Affinity and Anti-Affinity

Pod affinity and pod anti-affinity allow you to specify rules about how pods should be placed relative to other pods.

The rules are defined using custom labels on nodes and label selectors specified in pods.

Pod affinity/anti-affinity allows a pod to specify an affinity (or anti-affinity) towards a group of pods it can be placed with.

The node does not have control over the placement.


# 4. Multi-Container PODs

Containers within Multi container pods share the same lifecycle which means they are created together and destroyed together. They share the same network space which means they can refer to each other as local host and they have access to the same storage volumes.

This way you do not have to establish volume sharing or services between the PODs to enable communication between them to create a multi container pod.

There are different Design Patterns of Multi-container PODs in Kubernetes such as the Ambassador, Adapter and Sidecar.

### 4.1 Ambassador container

A good example of a Sidecar pattern is deploying a logging agent alongside a web server to collect logs and forward them to a central log server.

### 4.2 Adapter container

Building on that example say we have multiple applications generating logs in different formats.

It will be hard to process the various formats on the central logging server.

So before sending the logs to the central server we would like to convert the logs to a common format.

For this, we deploy an Adapter container. The Adapter container processes the logs before sending it to the central server.

### 4.3 Ambassador container

So your application communicates to different database instances at different stages of development. A local database for development, one for testing and another for production.

You must make sure to modify this connectivity in your application code depending on the environment you are deploying your application to.

You may choose to outsource such logic to a separate container within your pod so that your application can always refer to a database at local host and the new container will proxy that request to the right database.

This is known as an Ambassador container.

# 5. Observability

Pod conditions:

* PodScheduled: T/F

* Initialized: T/F

* ContainersReady: T/F

* Ready: T/F

The ready conditions indicate that the application inside the pod is running and is ready to accept user traffic.

Check **Conditions** section in kubectl pod describe.

The service relies on the pods ready condition to route traffic. 

## 5.1 Readiness and Liveness probes

The kubelet uses **liveness probes** to know when to restart a Container. For example, liveness probes could catch a deadlock, where an application is running, but unable to make progress. Restarting a Container in such a state can help to make the application more available despite bugs.

The kubelet uses **readiness probes** to know when a Container is ready to start accepting traffic. A Pod is considered ready when all of its Containers are ready. One use of this signal is to control which Pods are used as backends for Services. When a Pod is not ready, it is removed from Service load balancers.

There are different ways a probe can be configured:

* For a http use httpGet option with the path and the port.

In case of a web application, It could be when the API server is up and running.

    httpGet:
        path: /healthz
        port: 8080
        httpHeaders:
        - name: Custom-Header
          value: Awesome

* For TCP use the tcpSocket option with the port.

In case of database, you may test to see if a particular TCP socket is listening.

    tcpSocket:
        port: 3306

* For executing a command specify the exec option with the command and options in an array format.

    exec:
        command:
        - cat
        - /tmp/healthy

The service relies on the pods ready condition to route traffic. Without the readiness probe configured correctly, the service would immediately start routing traffic to the new POD. That will result in service disruption to at least some of the users.

Instead, if the PODs were configured with the correct readiness probe, the service will continue to serve traffic only to the older PODs and wait until the new POD is ready.

Once ready the traffic will be routed to the new POD as well, ensuring no users are affected.

## 5.2 Container logging

The docker logs command shows information logged by a running container. The docker service logs command shows information logged by all containers participating in a service.

By default, docker logs or docker service logs shows the command’s output just as it would appear if you ran the command interactively in a terminal. UNIX and Linux commands typically open three I/O streams when they run, called STDIN, STDOUT, and STDERR.

In some cases, docker logs may not show useful information unless you take additional steps.

* If you use a logging driver which sends logs to a file, an external host, a database, or another logging back-end, docker logs may not show useful information.

* If your image runs a non-interactive process such as a web server or a database, that application may send its output to log files instead of STDOUT and STDERR.

You can overcome the mentionned above issues by overwriting the log files and causing logs to be sent to the relevant special device instead in the Dockerfile as follow:

    # forward request and error logs to docker log collector
    RUN ln -sf /dev/stdout /var/log/nginx/access.log \
	&& ln -sf /dev/stderr /var/log/nginx/error.log

You can check container logs as follow:

    docker logs <containerId>

    kubectl logs -c <containerName> -f <podName>

## 5.3 Monitor and Debug applications

Kubernetes does not come with a full feature built in monitoring solution.

However there are a number of open source solutions available today such as Metric Server, Prometheus, Elastic Stack and proprietary solutions like Datadog and Dynatrace.

The kubernetes is for developers of course as well as the certification requires only a minimal knowledge of monitoring Kubernetes.

So in the scope of this course we will discuss about the metrics server only. The other solutions will be discussed in the Kubernetes for Administrators course.

Heapster was one of the original projects that enabled monitoring and analysis features for Kubernetes. You will see a lot of reference online when you look for reference architectures on monitoring Kubernetes. However, Heapster is now deprecated and a slimmed down version was formed known as the Metric Server.

You can have one metric server per Kubernetes cluster. The Metrics Server retrieves metrics from each of the Kubernetes nodes and pods, aggregates them and store them in memory.

Note that the metric server is only an in-memory monitoring solution and does not store the metrics on the disk. And as a result you cannot see historical performance data. 


So how were the metrics generated for the PODs on these nodes?

Kubernetes runs an agent on each node known as the **kubelet** which is responsible for receiving instructions from the kubernetes API Master server and running PODs on the nodes.

The Kubelet also contains a subcomponent known as the **cAdvisor** or Container aAdvisor.

cAdvisor is responsible for retrieving performance metrics from pods and exposing them through the kubelet API to meet the metrics available for the metrics server.


View all events:

    kubectl get events --all-namespaces

List Events sorted by timestamp:

    kubectl get events –sort-by=.metadata.creationTimestamp


Get node resource usage

    kubectl top node

Get pod resource usage

    kubectl top pod

Get resource usage for a given pod

    kubectl top <podname> --containers

List resource utilization for all containers

    kubectl top pod --all-namespaces --containers=true

# 6. Pod Design

## 6.1. Labels, Selectors and Annotations

## 6.2. Rolling Updates & Rollbacks in Deployments

## 6.3. Jobs

## 6.4. CronJobs
