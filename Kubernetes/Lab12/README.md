## Environment Details
- **Node Hostname:** `nti-virtual-machine`
- **Kubernetes Version:** `v1.28.15`
- **Namespace:** `ivolve`
- **ConfigMap Name:** `ivolve-cm`
- **Secret Name:** `ivolve-secret`

---

## Step-by-Step Implementation Guide

### Step 1: Create the ConfigMap Manifest (`ivolveCM.yaml`)
Create the manifest to store non-sensitive MySQL configuration variables (`DB_HOST` and `DB_USER`):

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: ivolve-cm
  namespace: ivolve
data:
  DB_HOST: "mysql-headless.ivolve.svc.cluster.local"
  DB_USER: "ivolve_user"
Apply the ConfigMap manifest and verify it:

Bash
kubectl apply -f ivolveCM.yaml --validate=false
kubectl get configmap ivolve-cm -n ivolve -o yaml
Step 2: Encode Secret Credentials in Base64
Encode the raw password strings into Base64 format before putting them into the Secret manifest:

Bash
# Encode root password ('rootpassword123')
echo -n 'rootpassword123' | base64
# Output: cm9vdHBhc3N3b3JkMTIz

# Encode user database password ('userpassword123')
echo -n 'userpassword123' | base64
# Output: dXNlcnBhc3N3b3JkMTIz
Step 3: Create the Secret Manifest (ivolveSecret.yaml)
Create the Secret manifest using the Base64-encoded credential strings:

YAML
apiVersion: v1
kind: Secret
metadata:
  name: ivolve-secret
  namespace: ivolve
type: Opaque
data:
  MYSQL_ROOT_PASSWORD: cm9vdHBhc3N3b3JkMTIz
  DB_PASSWORD: dXNlcnBhc3N3b3JkMTIz
Apply the Secret manifest to the cluster:

Bash
kubectl apply -f ivolveSecret.yaml --validate=false
Verify the Secret creation and structure:

Bash
kubectl get secret ivolve-secret -n ivolve
kubectl describe secret ivolve-secret -n ivolve
Step 4: Verification (Optional Test Pod)
Deploy a temporary pod to verify that both the ConfigMap and Secret variables inject correctly as environment variables:

Bash
kubectl apply -f - --validate=false <<EOF "-c", "env && 'DB_|MYSQL_' - -- --force --grace-period="0" -E -it -n 3600"] Check Clean EOF Never Pod ["sh", ``` ```bash alpine apiVersion: command: config-test-pod configMapRef: container: containers: delete env envFrom: environment exec grep image: injected inside ivolve ivolve-cm ivolve-secret kind: kubectl metadata: name: namespace: pod pod: restartPolicy: secretRef: sleep spec: test test-container the up v1 variables |>

-->nti@nti-virtual-machine:~/DevOps-Labs/Kubernetes/Lab12$ kubectl get secret ivolve-secret -n ivolve
kubectl describe secret ivolve-secret -n ivolve
NAME            TYPE     DATA   AGE
ivolve-secret   Opaque   2      10s
Name:         ivolve-secret
Namespace:    ivolve
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
DB_PASSWORD:          15 bytes
MYSQL_ROOT_PASSWORD:  15 bytes
nti@nti-virtual-machine:~/DevOps-Labs/Kubernetes/Lab12$ kubectl get configmap ivolve-cm -n ivolve -o yaml
apiVersion: v1
data:
  DB_HOST: mysql-headless.ivolve.svc.cluster.local
  DB_USER: ivolve_user
kind: ConfigMap
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","data":{"DB_HOST":"mysql-headless.ivolve.svc.cluster.local","DB_USER":"ivolve_user"},"kind":"ConfigMap","metadata":{"annotations":{},"name":"ivolve-cm","namespace":"ivolve"}}
  creationTimestamp: "2026-10-26T22:34:24Z"
  name: ivolve-cm
  namespace: ivolve
  resourceVersion: "4481"
  uid: fb0e645f-e5ed-49a3-9396-4ac14c2b1856

