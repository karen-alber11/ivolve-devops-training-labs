Lab 9: Kubernetes Cluster Initialization and CNI Configuration

Objective

Initialize a single-node Kubernetes control plane on an Ubuntu virtual machine (nti-virtual-machine), configure the containerd runtime, set up kubectl, and deploy the Calico Container Network Interface (CNI) plugin.

Step 1: System Prerequisites & Kernel Modules

Before installing Kubernetes, ensure swap is disabled and required kernel networking modules are loaded.

# 1. Disable swap immediately and permanently
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab

# 2. Load required kernel modules
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# 3. Configure sysctl parameters for Kubernetes networking
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-ip6tables = 1
net.ipv4.ip_forward         = 1
EOF

sudo sysctl --system


Step 2: Install and Configure containerd

Configure containerd as the container runtime with the systemd cgroup driver enabled.

# 1. Install containerd
sudo apt-get update && sudo apt-get install -y containerd

# 2. Generate default containerd configuration
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml

# 3. Enable the systemd cgroup driver
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml

# 4. Restart and enable containerd service
sudo systemctl restart containerd
sudo systemctl enable containerd


Step 3: Install Kubernetes Tools (kubeadm, kubelet, kubectl)

Install version v1.28.15 of the Kubernetes control components.

# 1. Install dependencies
sudo apt-get install -y apt-transport-https ca-certificates curl gpg

# 2. Add Kubernetes apt repository ring and source list
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee /etc/sources.list.d/kubernetes.list

# 3. Update apt and install specific version
sudo apt-get update
sudo apt-get install -y kubelet=1.28.15-1.1 kubeadm=1.28.15-1.1 kubectl=1.28.15-1.1
sudo apt-mark hold kubelet kubeadm kubectl


Step 4: Initialize the Control Plane

Initialize the Kubernetes control plane specifying the Pod network CIDR required by Calico (192.168.0.0/16).

sudo kubeadm init --pod-network-cidr=192.168.0.0/16


Step 5: Configure kubectl Access

Set up the local kubeconfig file so your regular user account can communicate with the cluster API server.

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config


Step 6: Deploy the Calico CNI Network Plugin

Apply the Calico manifest to establish pod networking across the cluster.

kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/calico.yaml


Step 7: Enable Single-Node Workloads (Remove Control-Plane Taint)

Since this is a single-node lab environment, remove the default taint on the control-plane node so system pods and future lab deployments can schedule successfully.

kubectl taint nodes nti-virtual-machine node-role.kubernetes.io/control-plane:NoSchedule-


Step 8: Cluster Verification & Troubleshooting

Verification

Verify that your node status has transitioned to Ready and all control-plane/system pods are running.

# Check node health
kubectl get nodes
-->nti@nti-virtual-machine:~/DevOps-Labs/Kubernetes/Lab9$ kubectl get nodes
NAME                  STATUS   ROLES           AGE   VERSION
nti-virtual-machine   Ready    control-plane   33m   v1.28.15

# Check all pods across namespaces
kubectl get pods -A
-->nti@nti-virtual-machine:~/DevOps-Labs/Kubernetes/Lab9$ kubectl get pods -A
NAMESPACE     NAME                                          READY   STATUS    RESTARTS   AGE
kube-system   calico-kube-controllers-7ddc4f45bc-745t5      1/1     Running   0          25m
kube-system   calico-node-2w8nh                             1/1     Running   0          25m
kube-system   coredns-5dd5756b68-q2k4x                      1/1     Running   0          25m
kube-system   coredns-5dd5756b68-rrrql                      1/1     Running   0          25m
kube-system   etcd-nti-virtual-machine                      1/1     Running   4          26m
kube-system   kube-apiserver-nti-virtual-machine            1/1     Running   4          27m
kube-system   kube-controller-manager-nti-virtual-machine   1/1     Running   3          8m30s
kube-system   kube-proxy-wnk6j                              1/1     Running   0          25m
kube-system   kube-scheduler-nti-virtual-machine            1/1     Running   9          26m

