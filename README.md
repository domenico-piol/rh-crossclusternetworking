# Cross-Cluster networking Demo with OpenShift
The base for all following demos will be some multi-cluster setups in Red Hat's lab environment.
Check the respective section for details as different setups will be used per technology.

Also, you need an appropriate application. I use my own demo-application SCCStore, which is available here:

https://github.com/domenico-piol/SCCstore

By default, the demo-application is a multi-tier app running in the same namespace. For this demo, we will separate the database from the rest of the application into the second cluster or environment.

<p align="center">
  <img src="./diagrams/architecture-app.drawio.svg">
</p>

## Deployment of demo application
So, we will deploy the standard setup of the application (using kustomize) and then quickly adapt it as needed.

This said - as a first step - clone the SCCStore application repository.

    git clone https://github.com/domenico-piol/SCCstore.git

> [!IWARNING]
>Also, the demo-application requires **Red Hat OpenShift Serverless** to be installed! Don't forget that.

The application can now be deployed to each of the required namespaces in all clusters - see the respective section for the details. As said, we use kustomize for this, so in the SCCstore folder, run:

    oc project <MY NAMESPACE>
    oc apply -k kustomize/overlays/dev

For simplicity's sake, we will not modify the existing SCCStore application. The required modifications will also be described in the respective sections.

## Submariner
Submariner interconnects Kubernetes clusters through a single, unified network (Layer 3). 
Combined with service-discovery, this enables correct routing to the application’s service implementation no matter which cluster it resides in. 

Submariner has - in principle - no knowledge about the application. I creates a VPN-tunnel between the clusters.
Automatic service discovery is available, but one can restrict what can be consumed from other clusters - or more precisely, you must publish the available services.

It's simple, coarse-grained cluster to cluster communication. Beside exporting services, applications have very little additional effort.

> [!NOTE]
> And this is one of the limitations... Submariner supports only k8s-to-k8s cluster communication! 

### Steps
Submariner is a cluster-to-cluster VPN tunnel, not a micro-segment of an application. This said, setting up Submariner is something the cluster-admin needs to do: 
1. Create the OpenShift (or K8s) clusters. We need 2 workload and 1 hub-cluster (RH ACM). A workload cluster can be also a collocated hub-cluster.
2. Setup Submariner. Create a `ManagedClusterSet` containing the 2 workload clusters, create the credentials needed and then enable Submariner.
3. Once Submariner is set up correctly and connectivity is enabled, the applications can expose their services (`ServiceExport`) and use them across clusters (use the appropriate service name: `*.svc.clusterset.local`).

> [!IMPORTANT]
> &rarr; See Instructions for the demo **[here](submariner/README.md)**

### Pros and Cons
:thumbsup: Simple to use for applications, once centrally set up by cluster-admins  
:thumbsup: Centrally managed connectivity between clusters  
:thumbsup: Native performance (no protocol translation)  
:thumbsup: Secure, traffic between member clusters is encrypted via IPsec tunnel  
:thumbsup: Dataplane Transparency - making the cross cluster communication act as if it where on a single flat network  

:thumbsdown: Connectivity must first be enabled by cluster admin (a pro at the same time!)  
:thumbsdown: Complex initial setup  
:thumbsdown: Additional operational overhead for platform team  
:thumbsdown: K8s only solution  


## Red Hat Service Interconnect (Skupper)
Red Hat Service Interconnect - aka Skupper - solves the problem of enabling communication (on Layer 7) and service discovery between different types of application environments. Between Kubernetes clusters and/or other types of application environments, such as virtual machines and bare-metal servers.

Other than e.g. Submariner, Skupper is a high-performance lightweight AMQP (Advanced Message Queuing Protocol) message router. So it uses a messaging system instead of VPN tunneling. 
Hmmm... you remember the declared obsolete ESB (Enterprise Service Bus)?

### Steps
Skupper is an application level construct and actually creates a micro-segment containing the application services. his can be handled completely by the application team w/o cluster-admin permissions!

1. You need either 2 OpenShift clusters or a cluster and a legacy (VM) environment. Or a combination of both...
2. Get the Skupper CLI and initialize Skupper in each environment/namespace
3. Create the token on the client environment/namespace
4. setup the Link between client and server environments
5. expose the services needed on the backend
6. create the Skupper service to the backend on the client

> [!IMPORTANT]
>&rarr; See Instructions for the demo **[here](skupper/README.md)**

### Pros and Cons
:thumbsup: Simple setup and usage for applications, without the need for administrative privileges (application layer security)  
:thumbsup: Kubernetes native solution  
:thumbsup: Environment agnostic: k8s, VM, BareMetal  
:thumbsup: Load balancing, failover (route) and scalability features ensuring robust and high available solutions  
:thumbsup: Secure, mTLS for every single connection  

:thumbsdown: Virtual Application Network must be managed by the applications  
:thumbsdown: Can introduce some performance overhead compared to Layer 3 solutions  
:thumbsdown: Service Discovery limited to own managed application  
:thumbsdown: The Layer 7 approach can make network-level troubleshooting more complex  


## Red Hat Federated Service Mesh (Istio)
> [!IMPORTANT]
>&rarr; See Instructions for the demo **[here](istio/README.md)**
