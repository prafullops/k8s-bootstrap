# known assumptions

we are going to use kubernetes version 1.34 as basline so, we can also play arround version upgrade steps.

## Versions:
    kubernetes: v1.34
    kubelet: v1.34.9-1.1
    kubeadm: v1.34.9-1.1
    kubectl: v1.34.9-1.1
    runc: 1.3.4-0ubuntu1~22.04.1
    containerd: 2.2.1-0ubuntu1~22.04.1

note:
    kubeadm and kubelet are highly senistive to version upgrades so alwys lock version in apt using apt-held to prevent auto upgradation of versions on apt update command. 

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

Verify the MAC address and product_uuid are unique for every node
You can get the MAC address of the network interfaces using the command ip link or ifconfig -a
The product_uuid can be checked by using the command sudo cat /sys/class/dmi/id/product_uuid
It is very likely that hardware devices will have unique addresses, although some virtual machines may have identical values. Kubernetes uses these values to uniquely identify the nodes in the cluster. If these values are not unique to each node, the installation process may fail.

container runtime interface is main protocal for communication between kubelet and container runtime.
 
# scope fr more automation
automate container runtime and kubeadm installation along with nodes bootstraping

# Cluster setup
once all the nodes are up and running we can start setting up our kubernetes cluster 

## Install kubeadm and kubelet
follow steps here https://v1-34.docs.kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

## Install container runtime 
For running containers in nodes we will require container a container runtime also if if container runtime is not present in nodes then kubeadm will throw errors. https://v1-34.docs.kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

Apt repository for this is already added before along with kubeadmsystemc
sudo apt update; sudo apt install -y containerd

by deafult after k8s version 1.28 kubelet takes systemd as cgroup driver , but for container runtime it needs to be configured. You can find this file under the path /etc/containerd/config.toml.

sudo mkdir -p /etc/containerd
containerd config default | sed 's/SystemdCgroup = false/SystemdCgroup = true/' | sudo tee /etc/containerd/config.toml
sudo systemctl restart containerd

# Initialize cluster
<!-- Need  ip forwarding between interfaces on host to allow pod-to-pod communication -->
echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p

sudo kubeadm init --apiserver-advertise-address 192.168.0.100 --pod-network-cidr "10.244.0.0/16" --upload-certs

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

without networkplugin node will be in not ready state

# references
https://v1-34.docs.kubernetes.io/releases/version-skew-policy/
https://v1-34.docs.kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#version-skew-policy
https://v1-34.docs.kubernetes.io/blog/2017/10/software-conformance-certification/
https://v1-34.docs.kubernetes.io/docs/concepts/containers/cri/

package repository: https://v1-34.docs.kubernetes.io/blog/2023/08/15/pkgs-k8s-io-introduction/





## Upgradation
kubeadm: https://v1-34.docs.kubernetes.io/docs/tasks/administer-cluster/kubeadm/upgrading-linux-nodes/



## what we did so far

installed virtual box so it can provide virtualization solution and help vagrant create vms
executed vagrant up to initialize creation of vms with bridge networking within them to enable node to node communication. 
now we got our clean slate time to configure it for kubernetes cluster where out prerequisite is kubeadm and kubelet service and containerd alsong with proper cgroup driver settings(containerd as well as kubelet). 

require above three service sto initialize cluster system components since they will be  configured as container in ods by kubeadm.