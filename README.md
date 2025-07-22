# dynasafe-interview

## create cluster

1. [install docker](https://docs.docker.com/engine/install/ubuntu/), for kind  
[install mise](https://mise.jdx.dev/getting-started.html#installing-mise-cli) for runtime manage  

2. install runtime  
config in [mise.toml](mise.toml)
use to install kubectl,helm,kind
```bash
mise install
```

3. create kind cluster  
kind config in [kind/kind-config.yaml](kind/kind-config.yaml)  
in config file define 4 node, all use kubernetes 1.32.5  
- 1 control-plane node 
- 1 work node(infra)  
- 2 work node(application)  

work node add label `role` as node group  
infra node set `extraPortMappings` for docker forward port to work node  

then crate cluster by config  

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

4. connect to cluster
export kubeconfig and setup kubectl  
```bash
kind get kubeconfig > kind/kubeconfig.yaml

source <(kubectl completion bash)
complete -o default -F __start_kubectl k
alias k="kubectl"
```

test  
```bash
$ k version
Client Version: v1.32.7
Kustomize Version: v5.5.0
Server Version: v1.32.5
```

5. install metallb  
it's use to implement service type loadbalance  

check infra node ip(INTERNAL-IP)  
```bash
$ k get node --selector='role=infra' -o wide 
NAME          STATUS   ROLES    AGE   VERSION   INTERNAL-IP       EXTERNAL-IP   OS-IMAGE                         KERNEL-VERSION    CONTAINER-RUNTIME
kind-worker   Ready    <none>   20m   v1.32.5   192.168.252.195   <none>        Debian GNU/Linux 12 (bookworm)   6.14.0-1007-oem   containerd://2.1.1
```

check [charts/metallb/IPAddressPool.yaml](charts/metallb/IPAddressPool.yaml) .spec.addresses is setup infra node ip  
if not, update first  
in helm values file [charts/metallb/values.yaml](charts/metallb/values.yaml) set `nodeAffinity` to set speaker is running in infra node  
and config L2 mode  

```bash
helm repo add metallb https://metallb.github.io/metallb
helm install metallb metallb/metallb \
  --version 0.15.2 \
  -f charts/metallb/values.yaml \
  --create-namespace \
  --namespace metallb-system

k apply -f charts/metallb/IPAddressPool.yaml
k apply -f charts/metallb/L2Advertisement.yaml
```

6. install haproxy-ingress 
use haproxy to support ingress feature  
helm value file [charts/haproxy-ingress/values.yaml](charts/haproxy-ingress/values.yaml) setup  
- running in infra node  
- use service type loadbalance to expose ingress controller  
- monitor by prometheus  

```bash
helm repo add haproxytech https://haproxytech.github.io/helm-charts

helm upgrade --install haproxy-kubernetes-ingress haproxytech/kubernetes-ingress \
  --version 1.44.5 \
  -f charts/haproxy-ingress/values.yaml \
  --create-namespace \
  --namespace haproxy-controller
```

## install monitor
1. install metrics-server for HPA
helm value file [charts/metrics-server/values.yaml](charts/metrics-server/values.yaml) setup  
- running in infra node  
- ignore tls verify certificate  

```bash
helm repo add metrics-server https://kubernetes-sigs.github.io/metrics-server/
helm upgrade --install metrics-server metrics-server/metrics-server \
  --version 3.12.2 \
  -f charts/metrics-server/values.yaml \
  --create-namespace \
  --namespace kube-system
```

2. install prometheus,node-exporter,kube-state-metrics (access http://prometheus.example.local:8080/)
helm value file [charts/prometheus/values.yaml](charts/prometheus/values.yaml) setup  
- running in infra node  
- disable alertmanager (not used)  
- disable prometheus-pushgateway (not used)  
- expose by ingress  

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm upgrade --install prometheus prometheus-community/prometheus \
  --version 27.28.0 \
  -f charts/prometheus/values.yaml \
  --create-namespace \
  --namespace prometheus
```

3. monitor etcd  
because etcd cannot configure `listen-metrics-urls` other than 127.0.0.1:2381  
here install a reverse proxy to scrape metric  
```bash
helm upgrade --install etcd-proxy charts/nginx \
  -f charts/nginx/etcd-metrics.yaml \
  --namespace prometheus
```

4. start grafana  
use docker to running grafana, and use provisioning feature to config grafana datasource and dashboard   
```bash
docker compose -f grafana/docker-compose.yml up -d 
```

open http://127.0.0.1:3000 will see grafana is started  
![grafana](images/grafana.png)

## explain monitor


## demo application
use httpbin for demo
```bash
helm upgrade --install httpbin charts/httpbin
```

test
```bash
curl --resolve httpbin.chart-example.local:80:127.0.0.1 http://httpbin.chart-example.local/
```

## teardown
```bash
kind delete cluster -n kind
docker compose -f grafana/docker-compose.yml down -v 
```