# Cross-Cluster networking Demo with OpenShift
The base for all following demos will be some multi-cluster setups in Red Hat's lab environment.
Check the respective section for details.

Also, you need an appropriate application. I use my own demo-application SCCStore, which is available here:

https://github.com/domenico-piol/SCCstore

By default, the demo-application is a multi-tier app running in the same namespace. For this demo, we will separate the database from the rest of the application into the second cluster.

<p align="center">
  <img src="./diagrams/architecture-app.drawio.svg">
</p>

This said - as a first step - clone the SCCStore application repository.

    git clone https://github.com/domenico-piol/SCCstore.git

This said, create a namespace **in each of the workload-clusters**:

    oc new-project sccstore-dev

:warning: Also, the demo-application requires **Red Hat OpenShift Serverless** to be installed! Don't forget that.

The application can now be deployed to ech of the namespaces in both clusters. We use kustomize for this, so in the SCCstore folder, run:

    oc apply -k kustomize/overlays/dev

For simplicity's sake, we will not modify the existing SCCStore application. The required modifications will be described in the respective sections.

## Submariner
Submariner interconnects Kubernetes clusters through a single, unified network (Layer 3). 
Combined with service-discovery, this enables correct routing to the application’s service implementation no matter which cluster it resides in. 

Submariner has - in principle - no knowledge about the application. I creates a VPN-tunnel between the clusters.
Automatic service discovery is available, but one can restrict what can be consumed from other clusters - or more precisely, you must publish the available services.

It's simple, coarse-grained cluster to cluster communication. Beside exporting services, applications have very little additional effort.

> :memo: **Note**
> And this is one of the limitations... Submariner supports only k8s-to-k8s cluster communication! 

==&rarr; See Instructions for the demo [here](submariner/README.md)==

### Pros and Cons
:thumbsup: Simple to use for applications, once centrally setup
:thumbsup: Centrally managed connectivity between clusters
:thumbsup: Native performance (no protocol translation)
:thumbsup: Secure, traffic between member clusters is encrypted via IPsec tunnel
:thumbsup: Dataplane Transparency - making the cross cluster communication act as if it where on a single flat network

:thumbsdown: Connectivity must first be enabled by cluster admin (a pro at the same time!)
:thumbsdown: Complex initial setup
:thumbsdown: Additional operational overhead for platform team
:thumbsdown: K8s only solution


## Red Hat Service Interconnect (Skupper)
Red Hat Service Interconnect - or Skupper - solves the problem of enabling communication (on Layer 7) and service discovery between different types of application environments. Between Kubernetes clusters and/or other types of application environments, such as virtual machines and bare-metal servers.

Other than e.g. Submariner, Skupper is a high-performance lightweight AMQP (Advanced Message Queuing Protocol) message router. So it uses a messaging system instead of VPN tunneling. 
Hmmm... you remember the declared obsolete ESB (Enterprise Service Bus)?

==&rarr; See Instructions for the demo [here](skupper/README.md)==

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
==&rarr; See Instructions for the demo [here](istio/README.md)==
