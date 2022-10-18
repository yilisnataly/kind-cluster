# kind-cluster

To setup a cluster from scratch it has been decided to use the kind tool for running local Kubernetes clusters using Docker container “nodes”, as it is a lightweight implementation of kubernetes for local development.

# How to create a cluster by using settings file

```bash
kind create cluster --name wefox-challenge-cluster --config wefox-challenge-cluster.yaml
Creating cluster "wefox-challenge-cluster" ...
 ✓ Ensuring node image (kindest/node:v1.25.2) 🖼
 ✓ Preparing nodes 📦
 ✓ Writing configuration 📜
 ✓ Starting control-plane 🕹️
 ✓ Installing CNI 🔌
 ✓ Installing StorageClass 💾
Set kubectl context to "kind-wefox-challenge-cluster"
You can now use your cluster with:

kubectl cluster-info --context kind-wefox-challenge-cluster

Not sure what to do next? 😅  Check out https://kind.sigs.k8s.io/docs/user/quick-start/

$ kubectl get nodes
NAME                                    STATUS   ROLES           AGE   VERSION
wefox-challenge-cluster-control-plane   Ready    control-plane   50s   v1.25.2
wefox-challenge-cluster-worker          Ready    <none>          50s   v1.25.2
wefox-challenge-cluster-worker2         Ready    <none>          50s   v1.25.2

```
# How to delete a cluster
```bash
$ kind delete cluster --name wefox-challenge-cluster
Deleting cluster "wefox-challenge-cluster" ...
```
