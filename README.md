# Cross-Cluster networking Demo with OpenShift
The base for all following demos is a multi-cluster setup (incl. ACM) in our lab environment.
The used lab environment already includes an ACM hub-cluster and 2 workload-clusters. Submariner is already installed and configured.

To create the environment, use this template: 

https://catalog.demo.redhat.com/catalog?search=submariner&item=babylon-catalog-prod%2Fsandboxes-gpte.dr-cloud-cluster-binder.prod

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

The application can now be deployed to ech of the namespaces in both clusters. We use kustomize for this, so in the SCCstore folder, run:

    oc apply -k kustomize/overlays/dev

For simplicity's sake, we will not modify the existing SCCStore application. The required modifications will be described in the respective sections.

## Submariner Demo
See Instructions [here](submariner/README.md)

## Red Hat Service Interconnect (Skupper) Demo
See Instructions [here](skupper/README.md)

## Federated Istio Demo
See Instructions [here](istio/README.md)
