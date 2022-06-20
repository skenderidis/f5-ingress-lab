# F5 Ingress Lab

## Introduction

This repository contains the documentation and the manifests for the "F5 Ingress Lab" on the UDF platform which is maintained by the EMEA SA team.
Below you can find the network diagram for the lab.

<img src="https://raw.githubusercontent.com/skenderidis/f5-ingress-lab/main/setup/images/udf-lab.png">


The technologies that are being used for the lab are the following

| Name | Notes | IP Address | Access Methods |
|---|---|---|---|
| **BIGIP (15.1)** |  - | 10.1.1.4 asdasd | SSH/RDP/HTTPS |
| **NGINX KIC** | Runs as a pod in K8s | 10.1.1.5 | SSH |
| **CIS** | Runs as a pod in K8s | 10.1.1.5 | SSH |
| **GitLab** | asdsaas| 10.1.1.8 | SSH |
| **ArgoCD** | Runs as a pod in K8s and published through CIS| 10.1.1.8 | SSH |
| **Elasticsearch** | Run as a docker instance | 10.1.1.8:9200 | SSH |
| **Logstash** | Run as a docker instance | 10.1.1.8 | SSH |
| **Kubernetes Master** | -| 10.1.1.5 | SSH |
| **Kubernetes Node1** | -| 10.1.1.6 | SSH |
| **Kubernetes Node2** | -|  10.1.1.7 | SSH |
| **VSCode** | asdsaas|  10.1.1.7 | SSH |


Credentials are documented inside the UDF Summary page.

## Installation Details
