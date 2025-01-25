---
title: "Getting Started With Kubernetes"
description: "Guides how to get started with Parosly"
summary: ""
date: 2023-09-07T16:04:48+02:00
lastmod: 2023-09-07T16:04:48+02:00
draft: false
weight: 820
toc: true
seo:
  title: "" # custom title (optional)
  description: "" # custom description (recommended)
  canonical: "" # custom canonical URL (optional)
  noindex: false # false (default) or true
---

## Getting started with Helm

To deploy Parosly in Kubernetes, it is easy to use the provided [values.yaml](./values.yaml) file. This will 
deploy the [prometheus-community/prometheus](https://github.com/prometheus-community/helm-charts/tree/main/charts/prometheus)
chart including the Parosly application as a sidecar container inside the Prometheus pod.

**IMPORTANT:** When using the provided values.yaml, all Prometheus configurations specified in the prometheus.yml file can not be managed through Helm values. Instead, the configurations need to be managed via 
the [/configs API](https://docs.parosly.io/#tag/configs) or the web UI of the Parosly app.

### Get repository info
```shell
$ helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
$ helm repo update
```

### Install chart
```shell
$ helm install prometheus prometheus-community/prometheus \
--values https://raw.githubusercontent.com/parosly/parosly/main/docs/examples/kubernetes/helm/values.yaml
```

### Verify that everything is working properly
When the chart is installed successfully by executing the following commands, you should see similar output.
```shell
$ kubectl get pod -l app.kubernetes.io/component=server
NAME                                 READY   STATUS    RESTARTS   AGE
prometheus-server-54595bd74b-tr7fb   3/3     Running   0          1m

$ kubectl get svc prometheus-server
NAME                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
prometheus-server   ClusterIP   10.100.237.217   <none>        80/TCP    1m

$ kubectl logs -f -l app.kubernetes.io/component=server -c parosly
{"timestamp": "07 Dec 2024 10:23:14", "level": "INFO", "message": "Starting web management UI"}
{"timestamp": "07 Dec 2024 10:23:15", "level": "INFO", "message": "The connection to the Prometheus server has been established successfully!"}
{"timestamp": "07 Dec 2024 10:23:15", "level": "INFO", "message": "Successfully started metrics endpoint at /api-metrics path"}
{"timestamp": "07 Dec 2024 10:23:15", "level": "INFO", "message": "Server listening on 0.0.0.0:9090"}
```
Once the apps are ready to handle requests, you can start creating your first Prometheus rules via API.
> **Important:** As you can see, the type of service is `ClusterIP`, which means it's only accessible from within the 
Kubernetes cluster.

### Expose service locally
Port forward the service and open another tab for the next step.
````shell
$ kubectl port-forward svc/prometheus-server 9090:80
````

### Crating example alerting rule via API
> POST /api/v1/rules

**Request**
```shell
curl -i -XPOST 'http://localhost:9090/api/v1/rules' \
--header 'Content-Type: application/json' \
--data '{
  "data": {
    "groups": [
      {
        "name": "ServiceHealthAlerts",
        "rules": [
          {
            "alert": "HighCPUUsage",
            "expr": "sum(rate(cpu_usage{job=\"webserver\"}[5m])) > 0.8",
            "for": "5m",
            "labels": {
              "severity": "warning"
            },
            "annotations": {
              "summary": "High CPU Usage Detected",
              "description": "The CPU usage for the web server is {{ $value }}% for the last 5 minutes."
            }
          }
        ]
      }
    ]
  }
}'
```

**Response**
```
HTTP/1.1 201 Created
content-length: 99
content-type: application/json

{"status":"success","message":"The rule was created successfully","file":"453ee16d-6310-42e0-8d57-2857e27d250f.yml"}
```
Where the `453ee16d-6310-42e0-8d57-2857e27d250f.yml` is the randomly generated filename created by the Parosly server.

### Deleting alerting rule file via API
> DELETE /api/v1/rules/{file}

_Note that the filename in your example is different from the example below._

**Request**
```shell
curl -i -XDELETE 'http://localhost:9090/api/v1/rules/453ee16d-6310-42e0-8d57-2857e27d250f.yml'
```
**Response**
```
HTTP/1.1 204 No Content
content-type: application/json
```
You can find the complete API documentation [here](https://docs.parosly.io). Enjoy!

---

## Further reading

- Read [about how-to guides](https://diataxis.fr/how-to-guides/) in the Diátaxis framework
