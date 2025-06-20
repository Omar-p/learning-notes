- Previously, our focus has been deploying applications to individual machines
    [limited workflow]
    - K8s allows us to abstract the way in the individual machines and treat the 
      entire cluster as a single logical machine. the application becomes a first
      class citizen which enables us to manage them using high level abstractions.
      In k8s, we can describe a set of application and how they should interact with
      each other and the let k8s figure out how to make that happen.

- Versioning:
  k8s versions are expressed using semantic versioning
  a K8S version is expressed as MAJOR.MINOR.PATCH
  there is a new patch release whenever needed.
  - generally, there is a bout 2 to 4 weeks between patch releases, except when 
    a critical bug or vulunerability is found.
  - there is a new minor release approximately every 3 months
  - at any given time, 3 minor releases are maintained. 
   about 9 months maintaince for a minor release.  

cluster is a set of nodes
  node: can be VM or physical machine.

unlike vm, containers are not meant to host an os, containers are meant to run specific
 task or process
 default command  in entrypoint, defaut args in CMD both in json array
 override in pod definitation Enterypoint -> command
                              CMD -> args
  ENv variable in pod definiation
  containers:
    env:
      -name: KEY
       valueFrom:
         (configMapKeyRef|secretKeyRef)
-----                       
k8s node:
 control
   containerd <-- kubelet(r/w etcd)[k8s part that interfaces the container runtime] <-- 
   [scheduler|Api server|controller-manager(taking care of generic cluster ops)] <--- DB[etcd]
 worker 
 
 : 
  [infrastructure] + [os] + [container runtime] +  [kubelet kube-proxy]
kubelet kube-proxy : specific k8s processes 
  kubelet:
    - kubernetes agent run on every node, it's mission is to connect the node
       to the controlplace of the cluster .
    - perform liveness check[liveness probes] for the container running into the pods   
  kube-proxy: one of network component of k8s.      
  
  ------
                                                [etcd (key-value DB, SSOT)]     
                                                            |[gRPC]
  [Controller Manager(Controller Loops)] <-protobuf-> [API Server(REST API)] <--protobuf----> [Scheduler(Bind Pod To Node)]
  API Server, Scheduler, and Controller Manager are stateless. everything is stored in etcd[single source of truth]
  
  User communicate with API SERVER[mostly REST], API SERVER communicate with work nodes via their
   kubelet[PROTOBUF].
  Kubelet communicate with [container runtime](CRI)
  
  Pod is a group of containers working together run on the same machine has the same IP.
  
  Together or separate
    - should we put e.g. a web application server and a cache together?
      ("cache" being something like e.g. Memcached or Redis)
    - Putting them in the same pod means:
      - they have to be scaled together.
      - they can communicate very efficiently over localhost.
    - Putting them in different pods means:
      - they can scaled separately
      - they must communicate over remote IP addresses
    - Both scenarios can make sense, depending on our goals      
    
    apiVersion, Kind, metadata :- will be in every single api response from k8s
    
    Endpoints only have a plural form


general form of kubectl:
  kubectl VERB/ACTION KIND/TYPE NAME_OF_RESOURCE --FLAGS
  
  when we use kubectl logs --selector label -> for each matching pod, there will be a connection with the api server. 
    --max-log-request
---------------------
in a cloud native environment the application is not directly related to any server, so all information the application needs is stored in the cloud.
---------------------
master node is the brain of the cluster where all decisions are made.
  master node contains control plane[scheduler, api server, cluster store, contoller manager, cloud controller Manager: communicate with cloud provider via its api]

API SERVER:
  frontend to k8s control plane.
  all communications go through API server External and Internal
  Exposes Restful Api on port 443.
  Authentication & authorization check
Cluster Store:
  Stores configuration and state of the entire cluster.
  Distributed Key/Value data store 
  Single source of truth[etcd]
    - etcd is the backend for service discovery and stores cluster state and configuration.
Scheduler:
  Watches for new workload/pods and assigns them to a node based on several scheduling factors[is node healty ?, has enough resources?, port available?, affinity and anti affinity rules, etc]
Controller Manager:
  Daemon that manages the controller loop.
    -Controller of controllers.
      [node controller]whenever the current state don't match the desired 
      state. it react to reach to the desired state.[#of running nodes for 				     example]
  it has several controllers each one of them is responsible for managing/control part 	 of the system. [replica, endpoint, namepaces, node ...] controller.
Cloud controller manager:
  interact with underlying cloud provider.
    congigure[load balancer, storage, instance]

  Master runs all cluster's control plane services..
------------------------------------------------------------------
worker node do the work [running the application for example].
	-Vm or Physical machine running linux.
	-provides running environment for the application
	-contain pod
	-Components:
		Kubelet container-runtime kube-proxy
------------------------------------------------------------------------
kubelet:
  Main agent run on every single node.
  Recives pod definitions from api server.
  interacts with container runtime to run containers associated with the pod.
  reports node and pod state to the master node.
container-runtime:
  -pull image from container registries[docker hub, ecr, gcr, local registery...] 
  -starting containers [running container and abstract its management for k8s 
  -stop containers 
  - CRI - container runtime interface
	-for 3rd party container runtime[docker was the default for cri  but now deprecated and k8s using containerd]
Kube-proxy:
  agent runs on every node through DaemonSets
  Responsible for :
    Local cluster networking [make sure each node has a unique ip address]
    Routing network traffic to load balanced services.




   - kubectl config set-context --current --namespace=[name]   <- edit .kube/config
   
   u can access svc in specific namespace curl svc.ns
   
   - ns organize app, not isolate it.
    to isolate u can use NetworkPolicy

while working  with multiple clusters env insall [kube-ps1]. it shows 
 the current cluster/context/namespace    

to format output use --output/-o [wide|json|yaml]

when createing pod by default it get event scheduled -->pulled -->created --> started.

to execute command on pod
  kubectl exec [-it] [podName] -c [specify-container-if-pod-contain-more-than-one] -- [command]
to expose port for testing and experimenting purpose only. in prod we use services[NodePort Service]:
  kubectl port-forward [podName] [hostPort]:[containerPort]
	kubectl port-forward pod/[podName] [hostPort]:[containerPort]
	kubectl port-forward service/[podName] [hostPort]:[containerPort]
	kubectl port-forward anyResourceType/[podName] [hostPort]:[containerPort]

restartPolicy for the container in the pod not for pod itself.

to show k8s resources:
  kubectl api-resources
https://kubernetes.io/docs/reference/kubectl/cheatsheet/
-------------------------
Deployments
	- Manages release of new application
	- Zero downtime deployments
	- Create ReplicaSet. 
	- Manage pods with deployments
	Deployment Strategy:
	  - Recreate -> delete all pods running before creating the new for the next version [dangerous: downtime]
	  - Rolling Update: [default] forwarding traffic to previous version. until finish creation of the new one.
    keep in ur mind: when u change the ui, and the old & new pods are available. how will we deal with that?
	  spec.strategy.type=[Recreate|RollingUpdate]
    Recreate
      - useful when u cannot run multiple version for the application at the same time
          [DB schema]
	  spec.strategy.minReadySeconds : #seconds new pods should be ready to be considered healthy. default 0
    spec.strategy.revisionHistoryLimit: default 10
    spec.strategy.rollingUpdate.maxSurge -> extra pods above desired ones. default 25%
	  spec.strategy.rollingUpdate.maxUnavailable -> unavilable pods during update. default 25%
	read: [instructor use --save-config in creating a deploy, and --record]
    --record : record the command in the deployment revision history.
    --save-config: save configs in resource's annotations.
	- when we update a Deployment with a new Pod definition:
	  - a new Replia Set is created with the new pod definition.
	  - that new Replica Set is progressively scaled up.
	  - meanwhile, the old Replica Set(s) is(are) scaled down.
	
	  
ReplicaSet always make sure that the desired number of pods is always running.

*to pause rollout
 kubectl rollout pause deployment [name]
 k set image deploy/frontend simple-webapp=kodekloud/webapp-color:v2
-
kubectl rollout history deploy [name]

describe specific one:
  kubectl rollout history deploy [name] --revision=[]

status of rollout:
   kubectl rollout status -f 
   kubectl rollout undo -f 
   kubectl rollout undo deploy [name] --to-revision=[]

metadata -> allows us to specify labels to access our resources.

  kubectl scale deploy [name] --replicas={num} -> scale the #of current running replicas.
  kubectl scale  deploy [name] edit the #of replicas manually.
  start imperatively
    kubectl create deploy .. --replicas

change in #of replicas is not reflected in rollout history because it is not a change in the app
  it's change in how k8s is dealing with the app.
=======
Imagine we have n nodes, one node is down.
  k8s will run the pods that were on the unavaiable node on the other nodes.
    when the node become. now it's empty. k8s doesn't re-balance the pods on the nodes
     when we scale up, because that node is empty. k8s will schedule all pods on that node.
=======
DaemonSet and  Stateful set
  - a DaemonSet is  a deploy that starts one po instance on every node in the cluster
    - when nodes are added or removed, the DaemonSet automatically change the #of Po accordingly

=======
AutoScaling:
  - In real clusters, Po are often automatically scaled based on resource usage properties that
    are collected by the metrics server.
  - The Horizontal po autoscaler observes usage statistics and after passing a threshold, will
    add additional replicas
=====    
ReplicaSet
  - ensure desired number of pods always running using [control loop].
  deployment --create --> replicatset.
  	- ReplicaSet implement a background control loop checks the desired #of pods are always present on the cluster.
  	to rollback:
  	 kubectl rollout undo deployment [name] --to-revision=[revision]
	  to view history:
	    kubectl rollout history deployment [name] --revision=
    k8s by default keep 10 revision of ur deployments to change that. spec.revisionHistoryLimit
    - replicasets give full compatibility with previously use replicationcontrollers
  
======
Observability: 
  - pod status [Pending(when scheduler try to figure out where to place the pod ),
                ContainerCreating,
                Running 
               ]
        conditions arr of T or F: [PodScheduled, Initialized, ContainerReady, Ready]
          - Ready : indicate app inside the pod is running and ready to accept traffic.
          - service rely on pod Ready cond to route traffic.
          Readiness Probes: in case of web app u can hit an endpoint to check, 
             in case of db u can check if a particular port is listening,
             using exec to run script inside a container to exist successfully if the app is ready.
             under Containers def> readinessProbe:
                        tcpSocket    httpGet:             exec:
                          port: 5432   path: /api/ready     command: 
                                       port: 8080             - cat
                                                              - /app/is_rea
            - U CAN set an initial time to wait initialDelaySeconds:, periodSeconds, failureThreshold[3]
          Liveness Probes: same as readiness but to check if the app inside the container remain healthy.

    Monitoring:minikube addons enable metrics-server
    kubectl top node: This provides the CPU and memory consumption of each of the nodes.
    kubectl top pod: view performance metrics of pods in Kubernetes.
======


  - node level metrics[#nodes in cluster, resource consumption]
  - pod level metrics [#pods, resource consumption]
  Metrics Server: save in-mem. 
    kubelet contains cAdvisor, cAdvisor is responsible for retrieving performance metrics from pods 
     and exposing them through the kubelet API to meet the metrics available for the metrics server. 
=====  
taint and toleration
  taint -> node.
=======
Node Affinity:
  - to ensure some pods deployed on a particular nodes.
===========
Services
  - a stable endpoint to connect to a pod or a group of pods.
  - expose a logical set of pods.
  - applying round-robin load balancing to forward traffic to specific pods.
  - set of pods is targeted by a selector
  - stable IP address
  - Stable DNS Name
  - stable Port
  Types:
    - ![img_4.png](img_4.png)
  - ![img_5.png](img_5.png)
  - ![img_6.png](img_6.png)
  - ![img_7.png](img_7.png)
  - ![img_8.png](img_8.png)
  -  kube-controller-manager will continuously scan for pods that match the selector and include these in the service
 
 Decoupling:
   - service exist independently from the [deployment]app they provide access to.
   - the only thing they do, is watch for Pods that have a specific label set
     matching the selector that is specified in the svc
   - that mean that one service can provide access to pods in multiple deploy,
     and while doing so, k8s will automatically load balance between these pods
      [used in canary deploy].  
---
- ClusterIP:
  - Exposes the Service on a cluster-internal IP. Reachable only from within the cluster. Kubernetes uses a round-robin
    algorithm to distribute traffic evenly among the targeted Pods.
- The `kubelet` makes the cluster IP address and port for every active Service available as environment variables. 
  The naming convention for Service-related environments variable are `<SERVICE_NAME>_SERVICE_HOST` and `<SERVICE_NAME>_SERVICE_PORT`.
  - `kubectl exec -it podName -- env`
    `PODNAME_SERVICE_HOST=10.96.254.0`
    `PODNAME_SERVICE_PORT=8080`
  - You need to make sure that the Service has been created before starting a Pod if you want those environment variables populated.
- service: NodePort:
  - Exposes the Service on each node’s IP address at a static port. Accessible from outside the cluster. 
    The Service type does not provide any load balancing across multiple nodes.
  - Allow you open a port on all nodes
  - Allowed Port Range: [30000-32767]
  - if client choose to send the req to Node with no healthy pods, Node responsible to send
     the req to be handled by another node with healty pods. 
  - spec.ports[port -> port service listening on, targetPort -> container in service listening on, 
    nodePort: exposed port on the node] -> if not specified, it will be chosen randomely.
    if u specify a port the targetPort will take the same val if it is not be specified 
    - nodePort must be unique.
  - Under the hood: kube-proxy sets up a bunch of iptables rules on our nodes****
  - Sometimes, it's the only available option for external traffic
    (e.f. most clusters deployed with kubeadm or on-premises)  
  DisAdvantages:
   - One service per port.
   - If Node IP address change then we have a problem.
   - ![img_9.png](img_9.png)
   - for exam:
     - creating pod and service:
        - `kubectl run echoserver --image=k8s.gcr.io/echoserver:1.10 --restart=Never \
  --port=8080 --expose`
   - Kubernetes represents endpoints by a dedicated primitive:
     - `kubectl get endpoints serviceName`

=====
LoadBalancer
   - Exposes the Service externally using a cloud provider’s load balancer.
  - An external load balancer is allocated for the service (typically cloud load balancer, AWS..
  - this is available only when the underlying infrastructure provides some kind of "load 
    balancer as aservice:
  - each service of that type will typically cost a little bit of money.
  - Ideally, traffic would flow directly from the load balancer to the pods.
  - In practice, it will often flow through NodePort first.  
  - Standard way of exposing Applications to the internet.
  - Create a load balancer per service.
  - AWS & GCP - Network Load Balancer NLB[cloud controller manager. handle that]. 
====
k8s default service [kubernetes]:
	- automatically created for us. to make us able to talk to k8s api from and within the app.
	- all communication go via kubernetes-api-server[kube-apiserver-minikube]
===
Labels:
  - kubectl get pods --show-labels
  spec.template.metadata.labels[app|environment|tier]
  select pods:
  	spec.selector.matchLabels[key:value]
  	all match or nothing 
  - kubectl get pods --selector="key=value,.." === kubectl get pods -l key=value,k2=v2  
  - kubectl get po -l 'labelKey1 in (v1, v2), labelKey2 notin (v1)' 
  
  - deploy find its pods using selector
  - svc find its endpoints pod using a selector 
  - used to facilitate resource management and selection
===
annotations: 
  Annotations is an unstructured key/value map stored with resource that may be set by external tools to store 
  and retrieve arbitary metadata, they are not querable and should be preserved when modifying objects. [used by external tools]
  spec.template.annotations
===
DNS:
  - k8s use coreDNS
  - each pod has a file /etc/resolv.conf contains the ip of the nameserver
-----
when a sa is created, it automatically create a token that used by the external
  app to authenticate when access to k8s api - create a secret resource to store the token.

etcd is a consistent and highly-available key value store used as Kubernetes' backing store for all cluster data.

A Kubernetes cluster already comes with a ServiceAccount, the default ServiceAccount that lives in the default namespace. Any Pod that doesn’t explicitly assign a ServiceAccount uses the default ServiceAccount.

 the API server creates a Secret holding the API token and assigns it to the ServiceAccount. The Secret and token names use the ServiceAccount name as a prefix. 

  - kubectl create serviceaccount build-bot
  
--declarative--
apiVersion: v1
kind: ServiceAccount
metadata:
  name: build-bot
----
to use kubectl without providing  the key,cert,ca 
 u set them in .kube/config file and they will be provided by default for every commands.
 in config file u define ur clusters, users, context[which user and which cluster]
 u don't create any new user in config , u add the already existing one
 
 current-context: user@cluster
  
  cmd:
    kubectl config view
    k config use-context user@cluster -> will update current-context field in the config file.
    k config -h 

O'relliy

RBAC:
  - RBAC consists of three key building blocks.
			Subject
				The user or process that wants to access a resource[ user, a group, or a ServiceAccount.]
			Resource
				The Kubernetes API resource type (e.g., a Deployment or node)
			Verb
				The operation that can be executed on the resource (e.g., creating a Pod or deleting a Service)

Authentication strategy	------ Description
X.509 client certificate  ------- Uses an OpenSSL client certificate to authenticate
Basic authentication -------- Uses username and password to authenticate
Bearer tokens --------- Uses OpenID (a flavor of OAuth2) or webhooks as a way to authenticate
---
The Role API primitive declares the API resources and their operations this rule should operate on.
The RoleBinding API primitive binds the Role object to the subject(s). 

To define new Roles and RoleBindings, you will have to use a context that allows for creating or modifying them, that is, cluster-admin or admin.

Roles and RoleBindings apply to a particular namespace. You will have to specify the namespace at the time of creating both objects. Sometimes, a set of Roles and Rolebindings needs to apply to multiple namespaces or even the whole cluster. For a cluster-wide definition, Kubernetes offers the API resource types ClusterRole and ClusterRoleBinding. The configuration elements are effectively the same. The only difference is the value of the kind attribute

	--verb for defining the verbs aka operations, and --resource for declaring a list of API resources.
	- kubectl create role read-only --verb=list,get,watch --resource=pods,deployments,services
	
--section rules lists the resources and verbs. --
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: read-only
rules:
- apiGroups:
  - ""
  resources:
  - pods
  - services
  verbs:
  - list
  - get
  - watch
- apiGroups:
  - apps
  resources:
  - deployments
  verbs:
  - list
  - get
  - watch
---------
-----Rolebinding--
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-only-binding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: read-only
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: johndoe
-----------
aggregate rules
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: []
  namespace: []
aggregationRule:
  clusterRoleSelectors:
  - matchLabels:
  - matchLabels:
  - matchLabels: ..
	
------
Default ClusterRole	----Description
cluster-admin:
		Allows read and write access to resources across all namespaces.

admin:
		Allows read and write access to resources in namespace including Roles and RoleBindings.

edit:
		Allows read and write access to resources in namespace except Roles and RoleBindings. Provides access to Secrets.

view:
		Allows read-only access to resources in namespace except Roles, RoleBindings, and Secrets.
	---
	kubectl explain [resource] -> show what to define in yaml. keys are case sensitive.
    explain pods.spec.volumes
	kubectl explain --recursive [resource] -> option for each keys
	kubectl apply -f [].yaml -> update mechanism not available for pod. presented to deploy
s
-------------------------
Multi-Container Pods: [logging, monitoring, syncing]
 The essence is that the main container and the sidecar container have access to shared resource to exchange information. [Volume ..]
  - sidecar container: a container that enhances the primary application, for instance for logging [Istio service mesh: monitor and shape traffic in a k8s env. fetch traffic statistics about the real app and istio injects sidecar in the real app]
  - Ambassador container: a container that represents the primary container to the outside world, such as a proxy.
  - Adapter container: used to adopt the traffic or data pattern to match the traffic or data pattern in other applications in the cluster.
  
  -- sidecar containers, etc. are not defined by a specific Pod propertites; from a k8s API resource, it's just a multi-container pod.
----
Init Container
  - initial container in a pod that complete a task before the "regular" container is started.
  - if init container has not run to completion, the main container is not started.
----
kubectl describe pod podname 
  to see all parameters and their current settings as they currently are in the [etcd -> store in json format] 
  ----
  to view pid in pod don't have ps
  cd /proc
  ls
  u will find pid
  cat {pid}/cmdline
  ---
  if shell is pid 1 and u run exit -> u will shut down the container so in this case use CTRL+P CTRL+Q
  ----
  logs
    - the pod entrypoint application does not connect to any STDOUT
    - Application output is sent to the k8s cluster.
    - use kubectl logs to connect to this output
------------
securtyContext:
  define privilage and access control settings 
  for a Pod or container, and includes the following:
    - Discretionary Access Control which is about permissions to access an object.
    - Security Enhanced Linux, where security labels can be applied
    - Running privileged or unprivileged user.
    - Using Linux capabilities.
    - AppArmor, which is an alternative to SELinux.
    - AllowPrivilegeEscalation which controls if a process can
      gain more privileges than its parent.
  use kubectl explain for a complete overview.
---- 
Managing Jobs
  - Pods normally created within deployment to run forever.
  - To create a Pod that runs up to completion, use Jobs instead. 
  - Jobs are useful for one-shot tasks, like [backup, calculation, batch processing and more]
  - Use spec.ttlSecondAfterFinished to clean up completed Jobs automatically.
  - create one or more Pods and ensures that a specified #pods successfully terminate.
  -ex: can be configured to run multiple pod in parallel; u need to get data from multiple servers.
  -ex: retrieve data from dbs and cache it in redis; backup; move data between DBs.
  - once a job is deleted its pods are removed.
Jobs Types:
  - 3 differents job types can be started, which 
    is specified by [completions] and [parallelism] parameters:
      - Non parallel jobs: One Pod is started, unless the pod fails
        - completions=1
        - parallelism=1
      - Parallel Jobs with a fixed completion count: the job is complete   
        after successfully running as many times as specified in jobs.spec.completions 
          - completions=n
          - parallelism=m
      - Parallel Jobs with a work queue: multiple Jobs are started,
        when one completes successfully, the job is complete.
          - completions=1
          - parallelism=n
  spec.template.spec.restartPolicy[Never|OnFailure]
  spec.completions:
  spec.parallelism:
  spec.activeDeadlineSeconds:
-----
CronJobs used for task that need to run on regular basis. 
   when running it a job being scheduled. job will start a pod.
   - names mist be less than 52 because k8s append some extra info.
   kubectl create cronjob every3mins --schedule="*/min hr dd " 
   cron format: * * * * *
                min hr (dayOfMonth) mm dayOfWeek(sunday=0..6)
                run at 22:30 every monday
                30 22 * * 1
                additional Cron Formats[non-standard]: @hourly @daily @monthly @yearly @weekly
                */30 * * * * every 30 mins
  spec.concurrencyPolicy: [Allow|Forbid|Replace] <== if job is running and the time to run other job pass, are u allow overlapping or not. 
  spec.schedule
  spec.jobTemplate
  .spec.backoffLimit to specify the number of retries before considering a Job as failed.
  ----------------------
   kubectl create deployment web --image=nginx
     - client send to API Server
     - API Server write to etcd [Depoyment] respond to the user deployment is created.
     - Controller manager notice the new deployment in etcd via API SERVER, and create 
       the replicaSet corresponding to that definiation, and write to etcd
     - another controller manager see the ReplicaSet through API SERVER create 
        it's pods in PENDING state.
     - Scheduler check if their is a resourc limit on the pending pod, 
       and assign it to worker node.
     - kublet on the worker node connect to the API SERVER and reported 
       to create the pod and respond to the API SERVER with the state of it.
       ------------
  K8s network model:
   - TL, DR:
     our cluster (nodes and pods) is one big flat IP network.
   - In detail:
     -   all nodes must be able to reach each other, without NAT.
     -   all pods must be able to reach each other, without NAT.
     -   pods and nodes must be able to reach each other, without NAT.
     -   each pod is aware of its IP address (no NAT)
     -   Pod IP address are assigned by the network implementation
  - kubernetes doesn't mandate any particular implementation
  --   
  1 the pod to pod network
  2 the pod to service network:
     - provide internal communication and load balancing.
     - is generally implemented with kube-proxy(or e.g. kube-router)
  3 Network policies
---
Resources Limitations
  - By default, a Pod will use as much CPU and memory as necessary to do its work.
  - This can be managed by using Memory/CPU requests and limits in 
    pod.spec.containers.resources
  - A request is an initial request for resources.
  - A limit defines the upper threshold of resources a Pod can use.
  - Memory as well as CPU limits can be used.
  - CPU limits are expressed in millicore or millicpu, 1/1000 of a CPU core
    - So, 500 millicore is 0.5 CPU
  - When being scheduled, the kube-scheduler ensures that the node running
    the Pods has all requested resources available
  - If a Pod with resource limits cannot be scheduled, it will show a status of
    Pending 
  
  - when using a deployment, use [kubectl set resources] to change resource limitations
    on running applications. u cannot do that for pods because pods don't updateable.  
  - pod/deployment resource limitations can be combined with quota on
    namespaces to restrict these applications in specific namespace only.
  
  - Try not to force resource to deletion, it may bring them in an
    unmanageable state.
    
    kubectl delete all --all
    don't do this: kubectl delete all --all -A --force --grace-period=-1
----
Networking in K8S
  - nodes are connected to each other via a [external network]node network
    - k8s create a software defined network that connects all nodes and pods.
        - there is an [internal network]pod-network connect all pods. each pod given an ip address.
        - there is an [internal] cluster network exposed to another network (external [node network]) used for administration.
    we have services that allow cluster network to access pod:
      - clusterIp: service available on the cluster net[used to expose internal compnents of the 
        app to the other components; for example DB]
      - nodePort: service on the cluster net; expose high port on nodes.
         - those node ports are redirecting to the node-port service side. 
      * now, user can access node on that port, but we don't user to be aware of the node ip so
        there is another service called Ingress the expose our app to the outside world  
      * ![img_3.png](img_3.png)
-------------
Ingress
  - Controller [contains LoadBalancer implementation] + API resource
    - controller connects to the cluster nodes.
    - as a resource in the cluster it has direct connection to the other svc
  - url resolver --via DNS--> connect to the controller in Ingress -> go to api resource -->
    now packets can be forwarded to the backend pods  
  - Ingress is used to provide external access to internal Kubernetes cluster resources
  - To do so, Ingress uses a load balancer that is present on the external cluster.
  - This Load Balancer is implemented by the ingress controller
  - As an API resource, Ingress uses selector label to connect pods that are used as a 
    service endpoints.
  - To access resource in the cluster, DNS must be configured to resolve to the Ingress
    Load Balancer IP.   
  - Ingress expose HTTP and HTTPS routes from outside the cluster to services within the 
    cluster   
  - Traffic routing is controlled by rules defined on the Ingress resource
    [which url redirect to which svc in the cluster]
  - Ingress can be configured to do the following:
    - give services externally-reachable URLs
    - Load balance traffic
    - Terminate SSL/TLS      
    - Offer name based virtual host 
  - Controllers:
    - Creating ingress resources without ingress controller has no effect.
    - Many Ingress COntroller exist:
      - nginx, haproxy, traefik, kong, contour.
  Rules:
    - An optional host. if no host is specified, the rule applies to all inbound 
      HTTP traffic.
    - A list of paths (like /testpath). each path has its own backend. Paths can 
      be exposed as a POSIX regular expression.
    - The backend, which consists of either a default backend in an Ingress 
      controller for incoming traffic that doesn't match a specific path.
  Ingress Backends:
    - A service backend relates to a k8s service.
    - A resource backend typically refers to cloud based object storage,
      where Ingress is used to provide access to storage resources
    - Resource backends and svc backends are mutually exclusive:
      - either u do it for svc, or u do it for resources              
    - A defaulttBackend can be set to handle traffic that doesn't deal with
      any specific backend.  
  Path Types:
    - The Ingress pathType specifies how to deal with path requests.
    - The Exact pathType indicates that an exact match should occur
      - if the path is set to /foo, and req /foo/ there is no match.
    - The Prefix pathType indicates taht the request path should start with
      - if the path set to /, any requested path woll match
      - if the path set to /foo, /foo as well as /foo/ will match
  Ingress Types:
    - Ingress backed by a single service: there is one rule that defines access
      to one backend Service
        - kubectl create ingress single --rule="/files=fileservice:80"
    - simple fanout: there are 2 or more rules defining different paths that refer
      to different services
        - kubectl create ingress single --rule="/files=fileservice:80" \
          rule="/db=dbservice:80"
   - Name-based virtual hosting: there are 2 or more rules that route requests       
     based on the host header
       - Make sure there is DNS entry for each host header
       .kubectl create ingress single --rule="my.examples.com/files*=fileservice:80" \
          rule="my.example.org/data*=dataservice:80"    
  IngressClass
    - used to set a specific ingress controller as the cluster default.
    - each ingress resource should specify a class, which refers to the cluster
      default IngressClass 
             
  Network Policies
    - By default, there are no restrictions to network traffic in k8s.
    - Pods can always communicate, even if they're in other NameSpaces
    - To limit this, Network Policies can be used.
    - Network Policies need to be supported by the network plugin though.
    - If in a policy there is no match, traffic will be denied. 
      - a network policy without any rules -> nobody can communicate.
    - If no Network Policy is used, all traffic is allowed. 
 Plugin for minikube [minikube start --cni=calico] -verify-> [kubectl get pods -n kube-system]
   Network Policy Identifiers:
     - when definig Pod- or NameSpace-based NetworkPolicy, a selector label is used to specify
       what traffic allowed to and from the Pods that match the selector
     - In Network Policies, three different identifiers can be used to mach network traffic
       - podSelector: allows access to a Pod with the corresponding selector label.
       - namespaceSelector: allows incoming traffic from namespaces with the matching
         selector label.
       - ipBlock: used to specify  a range of cluster-external IPs that should get access. 
    - Network Policies do not conflict , they are additive.      
     - if a network policy exists, all traffic is blocked. Unless there is a match. 
---------------
Storage:
  container can has a volume[only support pointing to(NFS or local storage)]
  container can has a volume point to any type of storage [..cloud storage, fiber channel, ISCSI]
  don't use HostPath in pv in prod env.
  
  - we can configure pods with locally configured volumes, but as App developer
    we want to distribute a manifest file and we want the mainfest to be able to
    work at our customer site as many customer as possible. so we are not going to 
    point any site-specific information in the manifest file. --> PVC come in, so we 
    won't point to a specific type of storage in the pod manifest. we'll point volume
    to PVC.
    - when u deploying a manifest on customer site u need its admin to create
      a persistence volume [a separate Api resource], allow site admin to connect
      to any site type of storage, when pod is deployed, it will bound to PV.
    * the last step is not a cloud friendly, that's where StorageClass comes in.
    - StorageClass: no matter which site u're using, any site if it's local cluster or
      an AWS cluster... all of those sites, they have their own specific storage.
   .. StorageClass automatically provisioning the storage for u. if PVC requests access
      to specific persistence volume and the specific persistence volume is not there, 
      it can request storageClass and SC will create a persistence volume on demand.
    * now, u don't need a site admin anymore. the only thing we do need is a site-soecific
      storage, being allocated by a provisioner. so storage provisioner need to be available.
        --> finding the right storage provisioner is sometimes challenging. 
  --in more formal way---
  - File stored in container will only live as long as the container itself.
  - Pod Volumes are used to allocate storage the outlives a container and
    stays available during pod lifetime.
  - Pod Volumes can directly bind to a specific storage type
  - Pod Volumes can also use persistent volume claims, to decouple
    the pod from the request site-specific-storage[size, r/w ].
  - A Persistent volume define access to external storage available in a specific 
    cluster.          
  - a wide rang of PV types is available.
  - Persistent Volume Claim(PVC) are used to connect to PV
  - when a PVC is created, it will search for an available PV that matches
    the storage request.
  - if a perfect match doesn't exist, StorageClass can automatically allocate it.  
  - in this scenario, the Pod uses the PVC storage type is completely decoupled
    from the site-specific storage.
  - A PVC bind is exclusive: after successfully binding to a PV, the PV cannot
    be used by other PVC anymore  
  ..StorageClass
  - provide a way for an admins to describe a class of storage, useful when u 
     are in an env with diff type of storage are available 
    - some cloud provide default StorageClass if u create a PVC without specifing StorageClass ur platform will set pvc storageClass to default
    - every cluster env]s have their own specific storage type .
    - kubernetes StorageClass allows for automatic provisioning of PVs when a 
      PVC request for storage comes in.
    - StorageClass must be backed by a storage provisioner, which takes care
      of the colume configuration.
    - Kubernetes comes with some internal provisioner, alternatively external 
      provisioner can be installed using Operators.    
  ..StorageClass..as..a.selector
    - Normally, PVC binding to a PV is based on best match.
    - While using Storage class in PV as well as PVC, a PVC will only bind to a PV
      that has the same StorageClass setting.
    - if such a PV is not available, the PVC will stay in a status of pending  
PVC(Persistence volume claim):
  read-writable or read only storage and a specific amount of storage.
    
-------------
pod local volume:
  pod.spec.volumes -> point to a specific volume type.
    --> for testing purpose emptyDir and hostPath are common
     emptyDir: a temp directory that will dynamically be created on the host that runs
               the pod.
     hostPath: a persistent directory that is defined and will outlive the lofetime of
               the pod.
     hostPath is  a little bit better than emptyDir
  The volume is mounted through pod.spec.containers.volumeMounts              
create deployments
expose svc
-----------------
decouple site specific information from ur generic app code

Providing Variables to kubernetes Application
  - k8s does not offer a command line option to provide variables while running 
    a deployment with [kubectl create deploy  ]
    - first, use kubectl create deploy mydb --image=mariadb
    - second, use kubectl set env deploy mydb KEY=VALUE
  - while running a Pod, env var acn be provided, but u shouldn't
    run naked Pods
      - kubectl run mydb --image=mysql --env="KEY=VALUE"  
Understanding the need for decoupling
  - It's good idea to separate site-specific information from code.
  - Code should be static, which makes it portable, so that it can be used in other
    environments
  - Variables are site-specific, and for that reason should not be provided in the
    the Deployment configuration.
  - K8s provides ConfigMaps to deal this issue.
  - A ConfigMap can be used to define variablesm and the Deployment just points to 
    the ConfigMap.
  - While using ConfigMaps, site-specifi information is included in the ConfigMap,
    which allows you to keep the Deployment manifest files generic.             
Understanding ConfigMap
  - ConfigMaps are used to provide different types of configuration to an application
  - ConfigMaps can be used for three different types of configuration
    - Variables
    - Configuration Files
    - Command Line arguments(not common to use cm for it)
    --> configMap created in different ways according to for what they are created.
  - Note that according to these different types of configurations, ConfigMaps are created
    and used in a very different way!
  - If an application refers to a ConfigMap, the ConfigMap should exist in the cluster 
    before running the applciation.  
    --
    env
    envFrom: 
      - configMapRef:
        name: cm
    --
    single env
    env:
      - name: KEY
        valueFrom:
          configMapKeyRef:
            name: cm-name 
            key:         
   --
   volumes:
   - name: app-config-vol
     configMap:
       name: app-config
----
Secret is a special type of configMap that add encoding                  
  - secret allow for storage of sensitive data such as pass, Auth tokens, and SSH Keys.
  - some secrets are automatically created by the system, users can also use secrets.
  - system-created secrets are important for k8s resources to connect to other cluster
    resources.
    types:
      - docker-registry used for connecting to docker registry <-
      - TLS used to store TLS key material
      - generic creates a secret from local file, directory or literal value. 
      
      kubectl create secret [type] ..
  ----
  - to access k8s API, all k8s resources need access to TLS keys.
     because at every single level, TLS key provide authorization between different
     k8s components, between ur pods and the backend svc
  - these key are provided by secrets and used through ServiceAccounts.
  
  - By using the ServiceAccount, the application gets access tp its secret/
  
  --
  Secrets in application 
  - There a different use cases for using secrets in applications:
    - To provide TLS keys to the application: 
       kubectl create secret tls my-tls-keys --cert=tls/my.crt --key=tls/my.key
    - To provide security to passwords:
       kubectl create secret generic my-secret-pw --from-literal=password=verysecret
    - To provide access to an ssh private key:
       kubectl create secret generic my-ssh-key --from-file=ssh-private-key=.ssh/id_rsa
     - to sensitive files which would be mounted in the app with root access only:
       kubectl create secret generic my-secret-file --from-file=/my/secret
https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/
https://kubernetes.io/docs/concepts/configuration/secret/#risks
       
----------
kustomize: 
  - base yaml, and then have overlay[for each deployment] patch on top of that yaml.
helm:
  - more powerful and complex. 
    - let us to write yaml template, and the helm will evaluate it before applying in k8s cluster
    - can used as package manager ,      




       
labels are limited in size, annotations have no limit
  labels can be used in selectors, annotations cannot.
      
----
KubeVirt technology addresses the needs of development 
  teams that have adopted or want to adopt Kubernetes but 
  possess existing Virtual Machine-based workloads that cannot 
  be easily containerized. More specifically, the technology provides 
  a unified development platform where developers can build, modify, 
  and deploy applications residing in both Application Containers 
  as well as Virtual Machines in a common, shared environment.
other use case for vm over container: high security app where the security 
  of hypervisor and certification are important.


Some other resources, such as namespaces, nodes, and PersistentVolumes,
 do not belong to namespaces and should have unique cluster-wide names.

 Service belongs to a namespace and gets a corresponding Domain Name Service (DNS) 
 record that has the namespace in the form of <service-name>.<namespace-name>.svc.cluster.local. 

 ResourceQuotas provide constraints that limit the aggregated resource consumption per namespace:
   ADMIN CAN  control the number of objects per type that are allowed in a namespace.[#SVC, #DEPLOY,..]
   ResourceQuotas can also limit the total sum of computing resources we can request in a given namespace. 

it is best to have enough labels to describe all important aspects of the Pods. For example, having labels to 
indicate the logical group of an application, the business characteristics and criticality, 
the specific runtime platform dependencies such as hardware architecture, or location preferences are all useful.


The information on the annotations is not intended for querying and matching objects. Instead, it is 
 intended for attaching additional metadata to objects from various tools and libraries we want to use.
  while labels are used primarily for query matching and performing actions on the matching resources, 
   annotations are used to attach metadata that can be consumed by a machine.

Predictable Demands pattern indicates how you should declare application requirements,
 whether they are hard runtime dependencies or resource requirements.  Declaring your 
 requirements is essential for Kubernetes to find the right place for your application within the cluster.

resource requirements, application runtimes also have dependencies on platform-managed capabilities
  like data storage or application configuration.

Volumes are an example of a runtime dependency that affects what kind of infrastructure 
 a Pod can run and whether the Pod can be scheduled at all.

The usage of a hostPort creates another runtime dependency on the nodes and limits where a Pod can be scheduled.
 hostPort reserves the port on each node in the cluster and is limited to a maximum of one Pod scheduled per node.

Configurations introduces a runtime dependency of your container to the named ConfigMaps. If not all of the expected ConfigMaps 
 are created, the containers are scheduled on a node, but they do not start up.

 Some of these dependencies limit where a Pod gets scheduled (if anywhere at all), 
  and other dependencies may prevent the Pod from starting up.

When designing your containerized applications with such dependencies, 
 always consider the runtime constraints they will create later.

Resources [compressible[cpu, bandwidth], incompressible[mem]]

(we define heap size for a Java application by using the -Xms and -Xmx command-line options.)

The requests amount (but not limits) is used by the scheduler when placing Pods to nodes. For a given Pod, the scheduler considers
 only nodes that still have enough capacity to accommodate the Pod and all of its containers by summing up the requested resource amounts.
 containers[].resources.requests : Initial resource request for CPU and memory.
 containers[].resources.limits : Upper limit until we want our application to grow at max. We don’t specify CPU limits by intention.

 memory:
   - This type is for the heap memory demands of your application, including volumes 
      of type emptyDir with the configuration medium: Memory.
      - containers that exceed their configured memory limit will trigger the Pod to be evicted;
 cpu:  it is highly recommended that you set requests for the CPU resource but no limits so that 
   they can benefit from all excess CPU resources     
 [hugepage-<size>, ephemeral-storage(emptyDir)]

Recommendations:
  For memory, always set requests equal to limits.
  For CPU, set requests but no limits

Depending on whether you specify the requests, the limits, or both, the platform offers three types of Quality of Service (QoS):
  Best-Effort[no req or limits, pods with no limits and req are the first pods killed when the node where the Pod is placed runs out of incompressible resources.]
  Burstable[req < limits: pods killed after Best-effort] 
  Guaranteed[req = limits: highest-priority Pods and are guaranteed not to be killed before Best-Effort and Burstable Pods. This QoS mode is the best option 
    for your application’s memory resources, as it entails the least surprise and avoids out-of-memory triggered evictions.] 

    So the resource characteristics you define or omit for the containers have a direct impact on its QoS and define 
    the relative importance of the Pod in the event of resource starvation. 
https://home.robusta.dev/blog/stop-using-cpu-limits
https://home.robusta.dev/blog/kubernetes-memory-limit
-- priority
 Pod priority affects the order in which the scheduler places Pods on nodes. 
  First, the priority admission controller uses the priorityClassName field to 
  populate the priority value for new Pods. When multiple Pods are waiting to be placed,
   the scheduler sorts the queue of pending Pods by highest priority first. 

   If there are no nodes with enough capacity to place a Pod, the scheduler can preempt (remove) 
   lower-priority Pods from nodes to free up resources and place Pods with higher priority.
    As a result, the higher-priority Pod might be scheduled sooner than Pods with a lower priority 
    if all other scheduling requirements are met. 
  
  Suppose you want your Pod to be scheduled with a particular priority but don’t want to evict any existing Pods.
   In that case, you can mark a PriorityClass with the field preemptionPolicy: Never
  

  --
  QoS is used primarily by the Kubelet to preserve node stability when available compute resources are low. 
   The Kubelet first considers QoS and then the PriorityClass of Pods before eviction. On the other hand, 
   the scheduler eviction logic ignores the QoS of Pods entirely when choosing preemption targets. 
   The scheduler attempts to pick a set of Pods with the lowest priority possible that satisfies the needs 
   of higher-priority Pods waiting to be placed.

  

  When Pods have a priority specified, it can have an undesired effect on other Pods that are evicted. 
   For example, while a Pod’s graceful termination policies are respected, the PodDisruptionBudget 
    is not guaranteed, which could break a lower-priority    clustered application that relies on a quorum of Pods.

  --
  Project Resources[ResourceQuota, LimitRange]
    working in a shared multitenanted platform also requires the presence of
     specific boundaries and control units to prevent some users from consuming all the platform’s resources
  LimitRanges help control the container resource profiles so that no containers require more resources than a cluster node
   can provide. LimitRanges can also prevent cluster users from creating containers that consume many resources, making the
    nodes not allocatable for other containers. 
     maxLimitRequestRatio: Maximum ratio limit/request, used to specify the allowed overcommit level. Here, the memory limit must not be
       larger than twice the memory request, and the CPU limit can be as high as four times the CPU request.
  --capacity planning
  it is evident that capacity planning for a multipurpose environment is not straightforward.
  Keep in mind that in the different environments, there are different numbers of containers, and you may even need
   to leave some room for autoscaling, build jobs, infrastructure containers, and more. 
---
- if you are looking for an easy way to create a statefulSet, you'll use the helm package manager.
  - because package manager make it easy to run applications in all required dependencies.
  - 
---
### Pod
- Pods add kubernetes properties to containers:
  - `nodeSelector` allow selection of the node that runs the pods.
  - `priority` allows for adding priority labels.
  - `restartPolicy` tells the cluster what to do if the pod fails.
  - you can run one or more containers in a pod.
  - pods can also include volume, which make it easy to provide storage containerized applications.
  - standalone pods are commonly used for testing and troubleshooting
  - standalone pod downsides:
    - no scaling
    - not rescheduled in case of failure
    - rolling updates are not possible, you can only bring pod down and bring it up again with the new settings.
  - `kubectl run podname --image=imageName`, `k describe pod podname`, `k logs podname`, `k exec -it podname -- /bin/bash`
- pod.spec.containers components:
  - `name`: name of the container
  - `image`: container image to run
  - `env`: environment variables to set
  - `volumeMounts`: volumes to mount
  - `command`: command to run
- multi-pod containers:
  - Sidecar container: a container that enhances the primary application, for instance for logging.
    - A sidecar container is providing additional functionality to the main container, where it makes no sense running this functionality in a separate Pod.
      - Think of logging, monitoring, and syncing.
      - The essence is that the main container and the sidecar container have access to shared resources to exchange information.
      - Istio service mesh is a common addition to Kubernetes that injects sidecar containers in Pods for traffic management.
      - Often, shared volumes are used for this purpose.
    - new definition in k8s docs:
      - A sidecar container is an initContainer that has the restartPolicy field set to Always.
      - It doesn't occur as a specific attribute, to create a sidecar you need to create an initContainer with the restartPolicy set to Always.
      - The sidecar container will be started before the main Pod is started and is typically used to repeatedly run a command.
      - Like a regular initContainer, the sidecar container must complete once before the main Pod is started.
  - Ambassador container: a container that represents the primary container to the outside world, such as a proxy.
  - Adapter Container: used to adopt the traffic or data pattern to match the traffic or data pattern
    to match the traffic or data patterns in other applications in the cluster.
- Init Container
  - An init container is a special case of a multi-container Pod, where the init container runs to completion before the main container is started.
  - Starting the main container depends on the success of the init container, if the init container fails the main container will never start
- Port forwarding:
  - A very simple way is by using port forwarding to expose a Pod port on the kubectl host that forwards to the Pod.
  - `kubectl port-forward fwnginx 8080:80`. Port forwarding is useful for testing Pod accessibility on a specific cluster node, not to expose it to external users.
  - Regular user access to applications is provided through Services and Ingress.
- RestartPolicy
  - The Pod restartPolicy determines what happens if a container that is managed by a Pod crashes.
  - If set to the default value restartPolicy=always, the container will be restarted after a crash.
  - restartPolicy=always does not affect the state of the entire Pod.
    - If the Pod is stopped or killed, restartPolicy=always won't restart it.
---
#### Jobs
- A Job starts a Pod with the restartPolicy set to never.
- To create a Pod that runs to completion, use Jobs instead.
- Jobs are useful for one-shot tasks, like backup, calculation, batch processing, and more.
- Use spec.ttlSecondsAfterFinished to clean up completed Jobs automatically.
- 3 different Job types can be started, which is specified by the completions and parallelism parameters:
  - Non-parallel Jobs: one Pod is started, unless the Pod fails
    - completions=1
    - parallelism=1
  - Parallel Jobs with a fixed completion count: the Job is complete after successfully running as many times as specified in jobs.spec.completions
    - completions=n
    - parallelism=m
  - Parallel Jobs with a work queue: multiple Jobs are started, when one completes successfully, the Job is complete.
    - completions=1
    - parallelism=n
---
### declarative way:
- All YAML manifest ingredients are defined as resource properties in the API. 
  - `apiVersion`: specifies which version of the API to use for the object.
  - `kind`: indicate the type of object (Deployment, Pod, etc.)
  - `metadata`: contains administrative information about the object.
  - `spec`: contains the specifics to define exactly how the resource is used.
  - `status`: added by the cluster for running resources
#### Namespaces
- Kubernetes Namespace resources leverage Linux kernel namespaces to provide resource isolation.
- Different Namespaces can be used to strictly separate between customer resources and thus enable multi-tenancy.
- Namespaces are used to apply different security-related settings,
  - Role-Based Access Control (RBAC)
  - Quota
- By installing complex Kubernetes applications in their own Namespace, managing them is easier.
---
- apply vs create:
  - `kubectl apply -f file.yaml` will create or update the resource.
  - `kubectl create -f file.yaml` will create the resource, fails if the resource already exists.
---
- commands:
- generating yaml file:
  - adds `--dry-run=client -o yaml > my.yaml` as an argument to the `k run` and `k create` comands.
    - `k run mynginx --image=nginx --dry-run=client -o yaml > my.yaml`
- show all resources in all ns, `k get all -A`
  - `k get all -n specific-ns`

---
Pods can be accessed in multiple ways.
---
CKAD NOTES:
- Remember, you CANNOT edit specifications of an existing POD other than the below.
  - spec.containers[*].image
  - spec.initContainers[*].image
  - spec.activeDeadlineSeconds
  - spec.tolerations
- if you asked to edit another fields you can use edit command to create a temp copy of the pod template, or extract pod definition as yaml and edit it, delete the old pod and create a new pod using the new definition.
- ![img.png](img.png)
- ![img_1.png](img_1.png)
- ![img_2.png](img_2.png)
- test svc :
  - `kubectl run tmp --image=busybox:1.36.1 --restart=Never -it --rm -- wget svcIPorSVCname:svcPort`
  - calling svc in another ns: `kubectl run tmp --image=busybox:1.36.1 --restart=Never -it --rm -- wget svcName.svcNamespace:svcPort`
  - full hostname: `svcName.NamespaceName.svc.cluster.local`
---
# Ardanlabs
- it's important to handle signal properly in the container.
  - in k8s the container has a 30 sec to shutdown after receiving a SIGTERM signal.
  - imagine rolling update a deployment with 100 containers and assume new container start at 3 seconds:
    - 3 * 100 = 300 seconds to start the new containers and if you didn't handle the signal properly each old one will take 30 seconds to shutdown.
      - 30 * 100 + 300 = 55 minutes to complete the rolling update.
      - SO we have to handle the signal properly to reduce the time and decrease the duration of the feedback loop.


# Debugging Kubernetes (Linkedin Learning)

### Pods and Deployment
#### Pod Stuck in a pending state:
  - no nodes with enough free capacity
  - the pod has a `toleration` that no nodes are `tainted` with
    - `taints` are props applied onto nodes that pods must tolerate in order to be admitted into them.
    - `Effects` tell k8s what to do with pods on nodes that are missing tolerations for its taints. 
  - the pod is waiting on some other resource, like a persistent volume claim.
    - before entering a rabbit hole and checking the StorageClass. First, we need to make sure the problem
      is from the pvc, you can do that by deleting the pvc definition and see if the pod status changes from the PENDING.
  - `kubectl get events` list all events in the namespace.


# Book
- a cluster IP: a new type of virtual IP, a special IP address the system will load balance across all of the Pods that are identified by the selector.
  - Because the cluster IP is virtual, it is stable, and it is appropriate to give it a DNS
    address. All of the issues around clients caching DNS results no longer apply. Within
    a namespace, it is as easy as just using the service name to connect to one of the Pods
    identified by a service.
  - Cluster IPs are stable virtual IPs that load balance traffic across all of the endpoints
    in a service. This magic is performed by a component running on every node in the
    cluster called the kube-proxy
  - the kube-proxy watches for new services in the cluster via the API
    server. It then programs a set of iptables rules in the kernel of that host to rewrite
    the destinations of packets so they are directed at one of the endpoints for that
    service. If the set of endpoints for a service changes (due to Pods coming and going
    or due to a failed readiness check), the set of iptables rules is rewritten.
  - The cluster IP itself is usually assigned by the API server as the service is created.
    However, when creating the service, the user can specify a specific cluster IP. Once
    set, the cluster IP cannot be modified without deleting and re-creating the Service
    object.
- Ingress is a Kubernetes-native way to implement the “virtual hosting” pattern:
  - ” This is a mechanism to host many HTTP sites on a single IP
    address. Typically, the user uses a load balancer or reverse proxy to accept incoming
    connections on HTTP (80) and HTTPS (443) ports. That program then parses the
    HTTP connection and, based on the Host header and the URL path that is requested,
    proxies the HTTP call to some other program. In this way, that load balancer or
    reverse proxy directs traffic for decoding and directing incoming connections to the
    right “upstream” server.
  - The Ingress controller is a software system made up of two parts. The
    first is the Ingress proxy, which is exposed outside the cluster using a service of
    type: LoadBalancer. This proxy sends requests to “upstream” servers. The other
    component is the Ingress reconciler, or operator. The Ingress operator is responsible
    for reading and monitoring Ingress objects in the Kubernetes API and reconfiguring
    the Ingress proxy to route traffic as specified in the Ingress resource.
