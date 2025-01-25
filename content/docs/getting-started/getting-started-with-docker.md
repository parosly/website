---
title: "Getting Started With Docker"
description: "Guides how to get started with Parosly"
summary: ""
date: 2025-01-20T18:00:00+02:00
lastmod: 2025-01-20T18:00:00+02:00
draft: false
weight: 820
toc: true
seo:
  title: "" # custom title (optional)
  description: "" # custom description (recommended)
  canonical: "" # custom canonical URL (optional)
  noindex: false # false (default) or true
---

**The following prerequisites are required to get up and running with this tool:**

- Prometheus server's rules directory and configuration file (prometheus.yml) must be shared and accessible
- Prometheus lifecycle API must be enabled to allow requesting the /reload API

---

## Getting started with Docker Compose

First, clone the project, and then start up the services with Docker Compose.
```shell
$ git clone https://github.com/parosly/parosly.git
$ cd parosly/docs/examples/docker
$ docker compose up -d
```
After successfully starting up services, the Parosly server will be ready to accept requests.

### Crating example alerting rule via API
> POST /api/v1/rules
> 
**Request**
```shell
curl -i -XPOST 'http://localhost:5000/api/v1/rules' \
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
curl -i -XDELETE 'http://localhost:5000/api/v1/rules/453ee16d-6310-42e0-8d57-2857e27d250f.yml'
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

