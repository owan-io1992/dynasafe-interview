# httpbin

![Version: 0.1.6](https://img.shields.io/badge/Version-0.1.6-informational?style=flat-square) ![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square) ![AppVersion: latest](https://img.shields.io/badge/AppVersion-latest-informational?style=flat-square)

this chart help create a [httpbin](https://github.com/mccutchen/go-httpbin) for internal testing 
it's have multiple api 
please read official [doc](https://httpbingo.org) for more info 

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` | Affinity for pod assignment |
| fullnameOverride | string | `""` | Override the full chart name |
| gateway | object | `{"enabled":false,"gatewayClassName":"","gatewayName":"","gatewayNamespace":"","hosts":[{"host":"chart-example.local","paths":[{"path":"/","pathType":"PathPrefix"}]}],"listeners":[{"allowedRoutes":{"namespaces":{"from":"Same"}},"name":"http","port":8000,"protocol":"HTTP"}]}` | Gateway API configuration |
| gateway.enabled | bool | `false` | Enable gateway support |
| gateway.gatewayClassName | string | `""` | The name of the GatewayClass resource. In GKE, this should be "gke-l7-gxlb" for multi-cluster and "gke-l7-rilb" for single-cluster. In Istio, this should be "istio". |
| gateway.gatewayName | string | `""` | The name of an existing Gateway resource to bind to. If not specified, a new Gateway resource is created. |
| gateway.gatewayNamespace | string | `""` | The namespace of an existing Gateway resource to bind to. If not specified, the new Gateway resource is created in the same namespace as the chart. |
| gateway.hosts | list | `[{"host":"chart-example.local","paths":[{"path":"/","pathType":"PathPrefix"}]}]` | Hosts for the Gateway resource |
| gateway.listeners | list | `[{"allowedRoutes":{"namespaces":{"from":"Same"}},"name":"http","port":8000,"protocol":"HTTP"}]` | Listeners for the Gateway resource. The name of the listener should match an entryPoint on the Gateway Controller. |
| image | object | `{"pullPolicy":"IfNotPresent","repository":"mccutchen/go-httpbin","tag":"latest"}` | Container image configuration |
| image.pullPolicy | string | `"IfNotPresent"` | Image pull policy |
| image.repository | string | `"mccutchen/go-httpbin"` | Image repository |
| image.tag | string | `"latest"` | Overrides the image tag whose default is the chart appVersion. |
| imagePullSecrets | list | `[]` | Image pull secrets |
| ingress | object | `{"annotations":{},"className":"","enabled":false,"hosts":[{"host":"chart-example.local","paths":[{"path":"/","pathType":"Prefix"}]}]}` | Ingress configuration |
| ingress.annotations | object | `{}` | Ingress annotations |
| ingress.className | string | `""` | Ingress class name |
| ingress.enabled | bool | `false` | Enable ingress |
| ingress.hosts | list | `[{"host":"chart-example.local","paths":[{"path":"/","pathType":"Prefix"}]}]` | Ingress hosts |
| keda | object | `{"cooldownPeriod":120,"enabled":false,"maxReplicaCount":3,"minReplicaCount":1,"pollingInterval":60,"triggers":[{"metadata":{"value":"80"},"metricType":"Utilization","type":"cpu"}]}` | KEDA autoscaling configuration |
| keda.cooldownPeriod | int | `120` | KEDA cooldown period |
| keda.enabled | bool | `false` | Enable KEDA autoscaling |
| keda.maxReplicaCount | int | `3` | Maximum replica count for KEDA |
| keda.minReplicaCount | int | `1` | Minimum replica count for KEDA |
| keda.pollingInterval | int | `60` | KEDA polling interval |
| keda.triggers | list | `[{"metadata":{"value":"80"},"metricType":"Utilization","type":"cpu"}]` | KEDA triggers |
| nameOverride | string | `""` | Override the chart name |
| nodeSelector | object | `{}` | Node selector for pod assignment |
| podAnnotations | object | `{}` | Annotations to add to pods |
| podLabels | object | `{}` | Labels to add to pods |
| podSecurityContext | object | `{}` | Pod security context |
| replicaCount | int | `1` | Number of httpbin replicas to deploy |
| resources | object | `{}` | Resource limits and requests |
| securityContext | object | `{}` | Container security context |
| service | object | `{"port":8080,"type":"ClusterIP"}` | Service configuration |
| service.port | int | `8080` | Service port |
| service.type | string | `"ClusterIP"` | Service type |
| serviceAccount | object | `{"annotations":{},"automount":true,"create":true,"name":""}` | Service account configuration |
| serviceAccount.annotations | object | `{}` | Annotations to add to the service account |
| serviceAccount.automount | bool | `true` | Automatically mount a ServiceAccount's API credentials? |
| serviceAccount.create | bool | `true` | Specifies whether a service account should be created |
| serviceAccount.name | string | `""` | The name of the service account to use. If not set and create is true, a name is generated using the fullname template |
| tolerations | list | `[]` | Tolerations for pod assignment |
| volumeMounts | list | `[]` | Additional volumeMounts on the output Deployment definition. |
| volumes | list | `[]` | Additional volumes on the output Deployment definition. |

----------------------------------------------
Autogenerated from chart metadata using [helm-docs v1.14.2](https://github.com/norwoodj/helm-docs/releases/v1.14.2)
