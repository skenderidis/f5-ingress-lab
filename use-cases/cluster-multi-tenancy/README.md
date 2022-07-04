# Multi-tenant Cluster K8s

This use-case explains how we can build a multi-tenant kubernetes cluster from the ingress and load balancing perpective. This includes clusters shared by different users at a single organization, and clusters that are shared by per-customer instances of a software as a service (SaaS) kubernetes.

A multi-tenant cluster is shared by multiple users and/or workloads which are referred to as "tenants". The operators of multi-tenant clusters must isolate tenants from each other to minimize the damage that a compromised or malicious tenant can do to the cluster and other tenants.

## Multi-tenancy requirements
Typically the requirement of a multi-tenat K8s environments in terms of Ingress/Load-Balancing are the following:
1. Each tenant should be able to manage their Ingress Resrouces.
2. Ingress Resources of one tenant shouldn't not affect/interfere with another tenant's Ingress Resources even if they are using the same FQDNs.
3. Performance degradation on the Ingress Controller of one tenant (due to usage/attacks/misconfiguration) shouldn't affect the performance of the Ingress Controller on another tenant.
4. Each Tenant should have a different Network IP address(es) to differentiate them and being able to apply different policies outside of the K8s cluster. 
5. Each Tenant should be able to deploy multiple Load Balancing objects depending on their Ingress/K8s requirements.
6. Each Tenant Load Balancing IPs should be different.


## Proposed Architecture
In order to address the above requirements we have designed a 2-tier architecture. The 1st tier is based on NGNIX+ Ingress Controller and will be installed in each tentant while the 2nd tier is based on BIGIP/CIS that will have the role of the external load balancer.

<p align="center">
  <img src="https://raw.githubusercontent.com/skenderidis/f5-ingress-lab/main/use-cases/cluster-multi-tenancy/multi-tenancy.png" style="width:75%">
</p>

### NGINX+ Ingress Controller



### BIGIP / CIS (external Load Balancer)


## Demo 

