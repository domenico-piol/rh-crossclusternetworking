# Cross-Cluster networking Demo with Red Hat Service Interconnect (Skupper)
As the base environment we will use again an environment from the Red Hat demo lab. 

To create the environment, use this template: 
https://catalog.demo.redhat.com/catalog?search=skupper&item=babylon-catalog-prod%2Fsandboxes-gpte.ocp4-service-interconnect.prod

But again, let's briefly talk about how Skupper works.

## Skupper - a primer
Skupper creates a service network, linking services together across hybrid cloud (Layer 7). It is secured using mTLS and supports smart routing and dynamic load balancing.

The main component is the Skupper router, a high-performance and lightweight AMQP message router, which must be installed on any environment (cluster and/or VM's). Unlike Submariner, it does not simply create a VPN, but routes each service connection separately.

<p align="center">
  <img src="../diagrams/architecture-skupper-highlevel.drawio.svg">
</p>

Red Hat Service Interconnect (Skupper) allows legacy or “never-migrate applications to continue running independently in their original environments while new web-tier applications are deployed in the cloud. To the cloud-native apps running in the cloud, these “legacy” apps appear to be cloud-native apps as well.

There is a good article on how to connect a frontend on OpenShift with a legacy backend here:
https://developers.redhat.com/learn/connect-your-services-across-different-environments-using-red-hat-service-interconnect
