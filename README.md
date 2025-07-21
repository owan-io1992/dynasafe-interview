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

4. export kubeconfig and test connection
```bash
kind get kubeconfig > kind/kubeconfig.yaml

source <(kubectl completion bash)
complete -o default -F __start_kubectl k
alias k="kubectl" 

k version
```

5. install haproxy-ingress 

```bash
helm repo add haproxytech https://haproxytech.github.io/helm-charts

helm upgrade --install haproxy-kubernetes-ingress haproxytech/kubernetes-ingress \
  --version 1.44.5 \
  -f charts/haproxy-ingress/interview.yaml \
  --create-namespace \
  --namespace haproxy-controller
```


## install monitor

```bash
helm repo add metrics-server https://kubernetes-sigs.github.io/metrics-server/
helm upgrade --install metrics-server metrics-server/metrics-server \
  --version 3.12.2 \
  -f charts/metrics-server/interview.yaml \
  --create-namespace \
  --namespace kube-system
```


prometheus,node-exporter,kube-state-metrics (access http://prometheus.example.local:8080/)
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm upgrade --install prometheus prometheus-community/prometheus \
  --version 27.28.0 \
  -f charts/prometheus/interview.yaml \
  --create-namespace \
  --namespace prometheus
```

add scrape etcd metrics  
because etcd cannot configure listen-metrics-urls other than 127.0.0.1:2381
here install a reverse proxy to scrpe metric
```bash
helm upgrade --install etcd-proxy charts/nginx \
  -f charts/nginx/etcd-metrics.yaml \
  --namespace prometheus
```

start grafana 
```bash
docker compose -f grafana/docker-compose.yml up -d 

```


## teardown
```bash
kind delete cluster -n kind
```