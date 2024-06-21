Kubernetes Install on the RedHat 9


### Step 1: Setup Hostnames
``` bash
sudo vim /etc/hosts
    public_ip       dns_address

```

### Step 2: Update and Configure the Servers
``` bash
# Before we start installing the required packages, let’s update the package manager cache and install any pending updates
sudo dnf makecache --refresh
sudo dnf update -y
sudo reboot

```

### Step 3: Set SELinux in permissive mode (effectively disabling it)
``` bash
# Next, we need to configure some system settings for Kubernetes to work properly. Let’s start by disabling SELinux temporarily and modifying the configuration file to set SELinux to permissive mode: 
sudo setenforce 0
sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config


```

### Step 4: Configure Kernel Modules
``` bash
# Load the necessary kernel modules required by Kubernetes:
sudo modprobe overlay
sudo modprobe br_netfilter

# To make these modules load at system boot, create a configuration file:
sudo su
cat > /etc/modules-load.d/k8s.conf << EOF
overlay
br_netfilter
EOF

or 

# Configure persistent loading of modules
sudo tee /etc/modules-load.d/k8s.conf <<EOF
overlay
br_netfilter
EOF

## br_netfilter module is loaded
lsmod | grep br_netfilter

```

### Step 5: Configure Sysctl Settings
``` bash
# Next, we’ll set specific sysctl settings that Kubernetes relies on:
sudo su
cat > /etc/sysctl.d/k8s.conf << EOF
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sysctl --system

or 

# Ensure sysctl params are set
sudo tee /etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF

# Reload configs
sudo sysctl --system


```


### Step 6: Disable Swap
``` bash
# Kubernetes requires that swap be disabled on all cluster nodes to ensure optimal performance and stability. Follow these steps to disable swap on each server:

sudo swapoff -a
sudo sed -e '/swap/s/^/#/g' -i /etc/fstab

```


### Step 7: Install CRI (Containerd)
``` bash
# Add Docker CE Repository
sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo dnf makecache

# Install Containerd.io
sudo dnf install -y containerd.io

# Configure Containerd
sudo mkdir /etc/containerd
sudo sh -c "containerd config default > /etc/containerd/config.toml"

# check == true
cat /etc/containerd/config.toml | grep SystemdCgroup

# Updating 
sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml

# enable,restart, start and status
sudo systemctl enable --now containerd.service
sudo systemctl start containerd.service
sudo systemctl restart containerd.service
sudo systemctl status containerd.service


```

### Step 8: Configure Firewall Rules
``` bash
# We need to allow specific ports used by Kubernetes components through the firewall. Execute the following commands to add the necessary rules:

firewall-cmd --permanent --add-port={6443,2379,2380,10250,10251,10252,5473}/tcp
firewall-cmd --reload

or 

sudo firewall-cmd --zone=public --permanent --add-port=6443/tcp
sudo firewall-cmd --zone=public --permanent --add-port=2379-2380/tcp
sudo firewall-cmd --zone=public --permanent --add-port=10250/tcp
sudo firewall-cmd --zone=public --permanent --add-port=10251/tcp
sudo firewall-cmd --zone=public --permanent --add-port=10252/tcp
sudo firewall-cmd --zone=public --permanent --add-port=10255/tcp
sudo firewall-cmd --zone=public --permanent --add-port=5473/tcp

# These commands add firewall rules to allow traffic on the specific ports required by Kubernetes components

Port(s)	            Description
6443	            Kubernetes API server
2379-2380	        etcd server client API
10250	            Kubelet API
10251	            kube-scheduler
10252	            kube-controller-manager
10255	            Read-only Kubelet API
5473	            ClusterControlPlaneConfig API
```


### Step 9: Install Kubernetes Components
``` bash
# Create the Kubernetes repository configuration file 
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.30/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.30/rpm/repodata/repomd.xml.key
exclude=kubelet kubeadm kubectl cri-tools kubernetes-cni
EOF

# Refresh the package cache
sudo dnf makecache

# cersion list for dnf
sudo dnf list --showduplicates kubectl, kubeadm, kubelet

# install specific version
sudo dnf install -y kubelet-1.28.3-0 kubeadm-1.28.3-0 kubectl-1.28.3-0 --disableexcludes=kubernetes

# latest
sudo dnf install -y kubelet kubeadm kubectl --disableexcludes=kubernetes


```



### Step 10: Initialize the Kubernetes Control Plane
``` bash
# pull images
sudo kubeadm config images pull

# initial
sudo kubeadm init --control-plane-endpoint="kubernetes.dev.env.test:6443" --apiserver-advertise-address=XXX.XXX.XX.XX --node-name k8s-master-1 --pod-network-cidr=XXX.XXX.0.0/24


```

### Step 11: Copy Configuration File to User’s Directory
``` bash
# On the master node, copy the configuration file to the user’s directory by running the following commands

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

```