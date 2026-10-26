# Lab 11: Namespace Management and Resource Quota Enforcement

## Objective
Isolate application resources using a dedicated Kubernetes namespace (`ivolve`), enforce a maximum pod limit using a `ResourceQuota`, and verify that Kubernetes blocks any pod creations that exceed the limit.

---

## Environment Details
- **Node Hostname:** `nti-virtual-machine`
- **Kubernetes Version:** `v1.28.15`
- **Namespace:** `ivolve`
- **ResourceQuota Name:** `ivolve-quota`

---

## Step-by-Step Implementation Guide

### Step 1: Create the Namespace
Create a dedicated namespace to isolate application resources:
```bash
kubectl create namespace ivolve
Step 2: Create the ResourceQuota Manifest
Create a file named ivolveQuota.yaml to enforce a hard limit of 3 pods within the ivolve namespace:

YAML
apiVersion: v1
kind: ResourceQuota
metadata:
  name: ivolve-quota
  namespace: ivolve
spec:
  hard:
    pods: "3"
Apply the ResourceQuota manifest:

Bash
kubectl apply -f ivolveQuota.yaml --validate=false
Verify that the quota is successfully applied and initialized:

Bash
kubectl describe quota ivolve-quota -n ivolve
--> nti@nti-virtual-machine:~/DevOps-Labs/Kubernetes/Lab11$ kubectl apply -f ivolveQuota.yaml --validate=false
kubectl describe quota ivolve-quota -n ivolve
resourcequota/ivolve-quota created
Name:       ivolve-quota
Namespace:  ivolve
Resource    Used  Hard
--------    ----  ----
pods        0     3


Quota Enforcement & Verification Procedures
1. Deploy Test Pods Up to the Quota Limit
Deploy 3 pods sequentially to reach the namespace limit:

Bash
kubectl run test-pod-1 --image=nginx:alpine -n ivolve
kubectl run test-pod-2 --image=nginx:alpine -n ivolve
kubectl run test-pod-3 --image=nginx:alpine -n ivolve
Verify that all 3 pods are running and consuming the quota:

Bash
kubectl describe quota ivolve-quota -n ivolve
-->nti@nti-virtual-machine:~/DevOps-Labs/Kubernetes/Lab11$ kubectl describe quota ivolve-quota -n ivolve
Name:       ivolve-quota
Namespace:  ivolve
Resource    Used  Hard
--------    ----  ----
pods        3     3

2. Test Quota Exceedance Block
Attempt to deploy a 4th pod (test-pid-4) to confirm that Kubernetes blocks the creation request:

Bash
kubectl run test-pid-4 --image=nginx:alpine -n ivolve
-->nti@nti-virtual-machine:~/DevOps-Labs/Kubernetes/Lab11$ kubectl run test-pod-4 --image=nginx:alpine -n ivolve
Error from server (Forbidden): pods "test-pod-4" is forbidden: exceeded quota: ivolve-quota, requested: pods=1, used: pods=3, limited: pods=3

