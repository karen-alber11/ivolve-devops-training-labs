# Lab 10: Node Isolation Using Taints, Labels, and Tolerations

## Objective
Isolate workloads on a single-node Kubernetes cluster (`nti-virtual-machine`) using node labeling, custom taints, and pod tolerations to control pod scheduling behavior, while successfully managing system resource constraints.

---

## Environment Details
- **Node Hostname:** `nti-virtual-machine`
- **Control Plane IP:** `192.168.218.131`
- **Kubernetes Version:** `v1.28.15`
- **Container Runtime:** `containerd://2.4.2`
- **Network Plugin (CNI):** Calico `v3.26.1`

---

## Step-by-Step Implementation Guide

### Step 1: Configure `kubectl` Access
Set up the administrator kubeconfig for the local user:
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g)$HOME/.kube/config
Step 2: Install Calico CNI Network Plugin
Apply the Calico network manifest with validation bypassed to initialize cluster networking:

Bash
kubectl apply --validate=false -f [https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/calico.yaml](https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/calico.yaml)
Step 3: Label the Node
Apply the required project label to the cluster node for targeted workload selection:

Bash
kubectl label nodes nti-virtual-machine project=ivolve --overwrite
Step 4: Apply the Custom Workload Taint
Taint the node to restrict general pods from scheduling unless they possess a matching toleration:

Bash
kubectl taint nodes nti-virtual-machine workload=ivolveApp:NoSchedule --overwrite
Resource Management & Troubleshooting (Disk Pressure Mitigation)
To resolve automatic Kubelet DiskPressure taints caused by root partition utilization (/dev/sda3), the following system maintenance and cleanup commands were executed:

Bash
# Check disk partition usage and capacity
df -h

# Clean package caches and orphaned packages
sudo apt-get clean
sudo apt-get autoremove --purge -y

# Prune unused container images and build caches via containerd
sudo crictl rmi --all

# Vacuum system journal logs to free disk space
sudo journalctl --vacuum-size=50M

# Restart Kubelet service to clear disk pressure conditions
sudo systemctl restart kubelet
Pod Configurations & Manifests
1. Untolerated Pod (untolerated-pod.yaml)
This pod does not have the required toleration or node selector. It is expected to remain in a Pending state, blocked by the node taint.

YAML
apiVersion: v1
kind: Pod
metadata:
  name: untolerated-pod
spec:
  containers:
  - name: nginx
    image: nginx:alpine
2. Tolerated Pod (tolerated-pod.yaml)
This pod targets the node using a nodeSelector (project=ivolve) and includes the matching toleration for workload=ivolveApp:NoSchedule. It is expected to successfully schedule and transition to Running.

YAML
apiVersion: v1
kind: Pod
metadata:
  name: tolerated-pod
spec:
  nodeSelector:
    project: ivolve
  tolerations:
  - key: "workload"
    operator: "Equal"
    value: "ivolveApp"
    effect: "NoSchedule"
  containers:
  - name: nginx
    image: nginx:alpine
Deployment & Verification Procedures
1. Deploy Test Pods
Apply both pod manifests to the cluster:

Bash
kubectl apply -f untolerated-pod.yaml --validate=false
kubectl apply -f tolerated-pod.yaml --validate=false
2. Verify Node State, Labels, and Taints
Check that the node labels and taints are correctly registered:

Bash
kubectl get nodes --show-labels
kubectl describe node nti-virtual-machine
3. Monitor Pod Scheduling Behavior
Verify that the tolerated-pod runs successfully while the untolerated-pod remains pending:

Bash
kubectl get pods -o wide

---

### Quick Save Command
To recreate or update your `README.md` directly in your lab directory (`~/DevOps-Labs/Kubernetes/Lab10`), you can run this single command:

```bash
cat << 'EOF' > README.md
# Lab 10: Node Isolation Using Taints, Labels, and Tolerations

## Objective
Isolate workloads on a single-node Kubernetes cluster (`nti-virtual-machine`) using node labeling, custom taints, and pod tolerations to control pod scheduling behavior, while successfully managing system resource constraints.

---

## Environment Details
- **Node Hostname:** `nti-virtual-machine`
- **Control Plane IP:** `192.168.218.131`
- **Kubernetes Version:** `v1.28.15`
- **Container Runtime:** `containerd://2.4.2`
- **Network Plugin (CNI):** Calico `v3.26.1`

---

## Step-by-Step Implementation Guide

### Step 1: Configure `kubectl` Access
Set up the administrator kubeconfig for the local user:
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g)$HOME/.kube/config
Step 2: Install Calico CNI Network Plugin
Apply the Calico network manifest with validation bypassed to initialize cluster networking:

Bash
kubectl apply --validate=false -f [https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/calico.yaml](https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/calico.yaml)
Step 3: Label the Node
Apply the required project label to the cluster node for targeted workload selection:

Bash
kubectl label nodes nti-virtual-machine project=ivolve --overwrite
Step 4: Apply the Custom Workload Taint
Taint the node to restrict general pods from scheduling unless they possess a matching toleration:

Bash
kubectl taint nodes nti-virtual-machine workload=ivolveApp:NoSchedule --overwrite
Resource Management & Troubleshooting (Disk Pressure Mitigation)
To resolve automatic Kubelet DiskPressure taints caused by root partition utilization (/dev/sda3), the following system maintenance and cleanup commands were executed:

Bash
# Check disk partition usage and capacity
df -h

# Clean package caches and orphaned packages
sudo apt-get clean
sudo apt-get autoremove --purge -y

# Prune unused container images and build caches via containerd
sudo crictl rmi --all

# Vacuum system journal logs to free disk space
sudo journalctl --vacuum-size=50M

# Restart Kubelet service to clear disk pressure conditions
sudo systemctl restart kubelet
Pod Configurations & Manifests
1. Untolerated Pod (untolerated-pod.yaml)
This pod does not have the required toleration or node selector. It is expected to remain in a Pending state, blocked by the node taint.

YAML
apiVersion: v1
kind: Pod
metadata:
  name: untolerated-pod
spec:
  containers:
  - name: nginx
    image: nginx:alpine
2. Tolerated Pod (tolerated-pod.yaml)
This pod targets the node using a nodeSelector (project=ivolve) and includes the matching toleration for workload=ivolveApp:NoSchedule. It is expected to successfully schedule and transition to Running.

YAML
apiVersion: v1
kind: Pod
metadata:
  name: tolerated-pod
spec:
  nodeSelector:
    project: ivolve
  tolerations:
  - key: "workload"
    operator: "Equal"
    value: "ivolveApp"
    effect: "NoSchedule"
  containers:
  - name: nginx
    image: nginx:alpine
Deployment & Verification Procedures
1. Deploy Test Pods
Apply both pod manifests to the cluster:

Bash
kubectl apply -f untolerated-pod.yaml --validate=false
kubectl apply -f tolerated-pod.yaml --validate=false
2. Verify Node State, Labels, and Taints
Check that the node labels and taints are correctly registered:

Bash
kubectl get nodes --show-labels
kubectl describe node nti-virtual-machine
3. Monitor Pod Scheduling Behavior
Verify that the tolerated-pod runs successfully while the untolerated-pod remains pending:

Bash
kubectl get pods -o wide
-->nti@nti-virtual-machine:~/DevOps-Labs/Kubernetes/Lab10$ kubectl get pods -A
NAMESPACE     NAME                                          READY   STATUS    RESTARTS       AGE
default       tolerated-pod                                 1/1     Pending   0              8m52s
default       untolerated-pod                               0/1     Pending   0              9m4s
kube-system   calico-kube-controllers-5c4d59995d-t7bhb      1/1     Running   0              4m29s
kube-system   calico-node-nbw29                             1/1     Running   0              25m
kube-system   etcd-nti-virtual-machine                      1/1     Running   5              46m
kube-system   kube-apiserver-nti-virtual-machine            1/1     Running   20 (28m ago)   46m
kube-system   kube-controller-manager-nti-virtual-machine   1/1     Running   0              46m
kube-system   kube-scheduler-nti-virtual-machine            1/1     Running   13             35m

