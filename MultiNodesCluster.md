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
<img width="1706" height="172" alt="image" src="https://github.com/user-attachments/assets/e71c07fb-41d9-4d5d-a842-558b7ca159b3" />


```
kind export logs --name multicrontrolworkercluster
```
<img width="1121" height="140" alt="image" src="https://github.com/user-attachments/assets/91af0bb2-fbfa-43fe-a365-4d7b97e54a53" />

To save a specific location
```
kind export logs --name multicrontrolworkercluster /mnt/
```

### Debugging
<img width="1710" height="319" alt="image" src="https://github.com/user-attachments/assets/6538360a-52c8-46b3-90ea-e6993512d26c" />

Change the node labels from **node-role.kubernetes.io/control-plane=cp1** to **mydomain.io/role=control-plane**
<img width="552" height="57" alt="image" src="https://github.com/user-attachments/assets/8ac40101-48a1-46af-b087-94ab28ae81d7" />

<img width="469" height="75" alt="image" src="https://github.com/user-attachments/assets/eb2eba4d-0579-49af-8fa0-7f04e5fbaf09" />

After changing, encountered with another error 
<img width="1708" height="112" alt="image" src="https://github.com/user-attachments/assets/b88cb6cc-7040-4c36-a2dc-6a85aa758fb1" />







