## Pre-requisites
- A compatible Linux host. The Kubernetes project provides generic instructions for linux distributions based on Debian and Red Hat, and those distributions without a package manager.
- 2 GB or more of RAM per machine (any less will leave little room for your apps).
- 2 CPUs or more for control plane machines.
- Full network connectivity between all machines in the cluster (public or private network is fine).
- Unique hostname, MAC address, and product_uuid for every node. See here for more details.
- Certain ports are open on your machines. See here for more details.

First steps:
    For windows machine install virtualBox for provisioning virtual nodes on local machine
    Install vagrant to automate vms creation and configuration on virtualBox to quickly bootstrap underling infrastructure for out kubernetes cluster.