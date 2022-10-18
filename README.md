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
# Setting Up An Ingress Controller

The ingress controller will be used to be able to access to the application, which it works as inverse proxy, so will have to install it in our kubernetes cluster.

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```
Now we proceed to apply the following commands:

```bash
kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=90s
```
Once the ingress controller is installed, we need to deploy the ingress object to relate the host where we will be accessing.
```bash
kind: Pod
apiVersion: v1
metadata:
  name: foo-app
  labels:
    app: http-echo
spec:
  containers:
  - name: foo-app
    image: hashicorp/http-echo:0.2.3
    args:
    - "-text=hello: world"
---
kind: Pod
apiVersion: v1
metadata:
  name: bar-app
  labels:
    app: http-echo
spec:
  containers:
  - name: bar-app
    image: hashicorp/http-echo:0.2.3
    args:
    - "-text=bar"
---
kind: Service
apiVersion: v1
metadata:
  name: foo-service
spec:
  selector:
    app: http-echo
  ports:
  - port: 8081
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
spec:
  ingressClassName: nginx
  rules:
    - host: hello.wefox.localhost
    - http:
        paths:
          - pathType: Prefix
            path: "/foo"
            backend:
              service:
                name: foo-service
                port:
                  number: 8081
          - pathType: Prefix
            path: "/bar"
            backend:
              service:
                name: bar-service
                port:
                  number: 8081
```

Apply the manifest:

```bash
kubectl apply -f http-echo.yaml
```
