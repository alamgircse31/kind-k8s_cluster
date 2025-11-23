# Create a 3 + 3 nodes (3 control panel and 3 work nodes) cluster
```
# A cluster with 3 control-plane nodes and 3 workers
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4

# ---- Global Cluster Settings ----
networking:
  apiServerAddress: "0.0.0.0"          # Listen on all interfaces
  apiServerPort: 6443                  # Host port for apiserver
  disableDefaultCNI: false
  podSubnet: "10.244.0.0/16"
  serviceSubnet: "10.96.0.0/12"

kubeadmConfigPatches:
  - |
    kind: ClusterConfiguration
    apiServer:
      extraArgs:
        "feature-gates": "IPv6DualStack=true"

# ---- Nodes ----
nodes:

# ---------------- CONTROL PLANES ----------------
- role: control-plane
  # Port mapping example
  extraPortMappings:
    - containerPort: 30000
      hostPort: 30000
      listenAddress: "127.0.0.1"
      protocol: TCP
  extraMounts:
    - hostPath: /data/kind
      containerPath: /var/lib/custom
  kubeadmConfigPatches:
    - |
      kind: InitConfiguration
      nodeRegistration:
        kubeletExtraArgs:
          node-labels: "node-role.kubernetes.io/control-plane=cp1"

- role: control-plane
  extraMounts:
    - hostPath: /data/kind
      containerPath: /var/lib/custom
  extraPortMappings:
    - containerPort: 30001
      hostPort: 30001
      listenAddress: "0.0.0.0"

- role: control-plane
  extraMounts:
    - hostPath: /data/kind
      containerPath: /var/lib/custom

# ---------------- WORKERS ----------------
- role: worker
  extraMounts:
    - hostPath: /data/kind
      containerPath: /var/lib/custom
  extraPortMappings:
    - containerPort: 8080
      hostPort: 8080
      listenAddress: "0.0.0.0"

- role: worker
  extraMounts:
    - hostPath: /data/kind
      containerPath: /var/lib/custom

- role: worker
  extraMounts:
    - hostPath: /data/kind
      containerPath: /var/lib/custom

```
```
kind create cluster --name multicrontrolworkercluster --config multicrontrolworkercluster.yaml
```
<img width="1705" height="212" alt="image" src="https://github.com/user-attachments/assets/3011c77e-5022-415b-af96-d67b51d557e7" />

> [!NOTE]
> This error mail comes due to Insufficient system resources. But after increasing the CPU and memory, observed the same error.

To debug the error, we run the below commands. 
```
kind create cluster --name multicrontrolworkercluster --config multicrontrolworkercluster.yaml --retain
```

> [!NOTE]
> --retain → Do NOT delete cluster nodes if cluster creation fails. We used this flag for debugging.

```
kind export logs --name multicrontrolworkercluster
```




