# dynasafe-interview

## create cluster

1. [install docker](https://docs.docker.com/engine/install/ubuntu/)  

2. install runtime  
```bash
mise install
```

3. create kind cluster
```bash
$ kind create cluster --config kind/kind-config.yaml
Creating cluster "kind" ...
 ✓ Ensuring node image (kindest/node:v1.32.5) 🖼
 ✓ Preparing nodes 📦 📦 📦 📦 📦 📦 📦  
 ✓ Configuring the external load balancer ⚖ 
 ✓ Writing configuration 📜 
 ✓ Starting control-plane 🕹 
 ✓ Installing CNI 🔌 
 ✓ Installing StorageClass 💾 
 ✓ Joining more control-plane nodes 🎮 
 ✓ Joining worker nodes 🚜 
Set kubectl context to "kind-kind"
You can now use your cluster with:

kubectl cluster-info --context kind-kind

Thanks for using kind! 😊

```

4. export kubeconfig 
```bash
$ kind get kubeconfig > kind/kubeconfig.yaml
```

5. install haproxy-ingress 

```bash
helm repo add haproxytech https://haproxytech.github.io/helm-charts

helm install haproxy-kubernetes-ingress haproxytech/kubernetes-ingress \
  -f charts/haproxy-ingress/interview.yaml \
  --create-namespace \
  --namespace haproxy-controller
```



