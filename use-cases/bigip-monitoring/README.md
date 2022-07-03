# Monitoring services published via BIGIP with Prometheus, Grafana and Elastic
In this section we go through how you can efectively to monitor K8s services that are being delivered by BIGIP/CIS with an observability platform. The technologies that are part of the observability platform are the following:
- Prometheus
- Logstash
- Elasticsearch
- Grafana
- Telemetry Streaming (F5)

The dashboards that have been created to assist with the monitoring of the K8s services are:
  - [**CIS Dashboard**](#cis-dashboard)
  - [**Client/Server SSL Performance**](#clientserver-ssl-dashboard)
  - [**HTTP Profiles**](#http-profiles) 
  - [**Pools**](#pools-dashboard)
  - [**VS Access Logs**](#vs-access-logs)
  - BIGIP Error Logs (**pending**)

## CIS Dashboard
This dashboard provides an overall view on the utilization and performance of the applications handled by BIGIP. 
The dashboard provides visibility on the following:
- Total number of Virtual Servers, Pools and Members on the BIGIP appliances
- HTTP Reponse Codes (2xx, 3xx, 4xx and 5xx) for the specific period across the entire appliance
- Pool availability (requires a monitor to be assinged to the pool)
- Virtual Servers statistics (with drill in capabiltiy)
- Pool statistics (with drill in capabiltiy)
- Client/Server SSL Profile stastics 
- HTTP Profile stastics 
- Utilization and L7 TPS over time

<img src="https://raw.githubusercontent.com/skenderidis/f5-ingress-lab/main/use-cases/bigip-monitoring/images/dashboard.png">


> The source of information used to create this dashboard is Prometheus.

## Client/Server SSL Dashboard
For both Client and Server SSL dashboards we check multiple SSL metrics, from the SSL version, to the cipher, to the key exhange used. We can verify any SSL failure and check if the SSL offloading is happening on hardware or software.
<img src="https://raw.githubusercontent.com/skenderidis/f5-ingress-lab/main/use-cases/bigip-monitoring/images/client-ssl.png">


## Pools Dashboard
In this dashboard we provide more details on the utilization and transaction for a specific pool and its memebers. You can also monitor Pool member's availability along with statistics and utilization over time. 
<img src="https://raw.githubusercontent.com/skenderidis/f5-ingress-lab/main/use-cases/bigip-monitoring/images/pools.png">


## HTTP Profile Dashboard
In this dashboard we get the HTTP response code (2xx, 3xx, 4xx, 5xx) per HTTP Profile and we can observe the behaviour over time.
<img src="https://raw.githubusercontent.com/skenderidis/f5-ingress-lab/main/use-cases/bigip-monitoring/images/http-profile.png">

## VS Access Logs
In this dashboard we get the HTTP response code (2xx, 3xx, 4xx, 5xx) per HTTP Profile and we can observe the behaviour over time.
<img src="https://raw.githubusercontent.com/skenderidis/f5-ingress-lab/main/use-cases/bigip-monitoring/images/access-logs.png">






**Telemetry Streaming**

Telemetry Streaming (TS) enables you to declaratively aggregate, normalize, and forward statistics and events from the BIG-IP to a consumer application. There are 2 type of consumers; Push and Pull Consumer. <br>
The Push consumer that that has been configured is sending all the access logs to ElasticSearch. The pre-requisite for this is to attach the `name` iRule on to the Virtual Server that you want to send the logs to Elastic. 
The Prometheus Pull Consumer has also been enabled in order to expose a new HTTP API endpoint that can be scraped by Prometheus for metrics. The Prometheus Pull Consumer outputs the telemetry data according to the Prometheus data model specification. 

**Prometheus**

Prometheus has been configured to scrape both BIGIP and CIS for all metrics every 30 seconds.

**Elasticsearch**

Elasticsearch is configure to received logs 


**BIGIP Access logs**

Prometheus has been configured to scrape both BIGIP and CIS for all metrics every 30 seconds


## Setup (pending)
The solution has already been deployed on 
