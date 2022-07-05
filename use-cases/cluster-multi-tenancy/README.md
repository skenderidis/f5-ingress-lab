# Multi-tenant Cluster K8s

This use-case explains how we can build a multi-tenant kubernetes cluster from the ingress and load balancing perpective. This includes clusters shared by different users at a single organization, and clusters that are shared by per-customer instances of a software as a service (SaaS) kubernetes.

A multi-tenant cluster is shared by multiple users and/or workloads which are referred to as "tenants". The operators of multi-tenant clusters must isolate tenants from each other to minimize the damage that a compromised or malicious tenant can do to the cluster and other tenants.

## Multi-tenancy requirements
Typically the requirement of a multi-tenat K8s environments in terms of Ingress/Load-Balancing are the following:
1. Each tenant should be able to manage their Ingress Resources.
2. Ingress Resources of one tenant shouldn't not affect/interfere with another tenant's Ingress Resources even if they are using the same FQDNs.
3. Performance degradation on the Ingress Controller of one tenant (due to usage/attacks/misconfiguration) shouldn't affect the performance of the Ingress Controller on another tenant.
4. Each Tenant should have a different Network IP address(es) to differentiate them and being able to apply different network policies outside the K8s cluster. 
5. Each Tenant should be able to deploy multiple Load Balancing objects depending on their Ingress/K8s requirements.
6. Each Tenant Load Balancing IPs should be different.


## Proposed Architecture
In order to address the above requirements we have designed a 2-tier architecture. The 1st tier is based on NGNIX+ Ingress Controller and will be deployed in each tentant while the 2nd tier is based on BIGIP/CIS that will have the role of the external load balancer.

<p align="center">
  <img src="https://raw.githubusercontent.com/skenderidis/f5-ingress-lab/main/use-cases/cluster-multi-tenancy/multi-tenancy.png" style="width:75%">
</p>

### Tier 1 - NGINX+ Ingress Controller
In our design we choose to have seperate NGINX+ Ingress Controller (IC) deployment per tenant. This design was prefered because of the following benefits:

- **Security.** By deploying seperate IC instances we are creating a full isolation for both dataplane and controlplane between tenants. This means that in situation of high-usage, attacks or even misconfiguration on a tenant's IC this will not affect tenant's deployments.  

- **Customization.** You can customize or fine tune your IC behavior through the use of Configmaps. For example, set the number of worker processes or customize the access log format. While this is very important for the application delivery, ConfigMap applies globally, meaning that it affects every Ingress resource. Therefore if applied across mulitple tenants, you cannot fine-tune these variables based on each tenant's requirements.

- **Management.** When sharing a single IC across multiple tentants, the responsibility for upgrading, patching, scaling, performance-tuning, etc is with the service provider. By having seperate IC per tenant the responsibility can be transfered or shared with the customers.

When running NGINX Ingress Controller, you have the following options with regards to which configuration resources it handles:

- **Single-namespace Ingress Controller (selected).** You can configure the Ingress Controller to handle configuration resources only from a particular namespace, which is controlled through the -watch-namespace command-line argument. 
- **Cluster-wide Ingress Controller.** The Ingress Controller handles configuration resources created in any namespace of the cluster.


### Tier 2 - BIGIP / CIS (external Load Balancer)
BIGIP role in the design is to publish the NGINX IC outside of the Kubernetes environment. To achieve that we are using CIS to discover the NGINX IC services and publish each service with with a different network VIP to the outside world.  

**Discovery**

In order for CIS to discover the NGINX IC services running in each tenant, it is required to deploy a CIS CRD (VirtualServer, TransportServer or IngressLink) on the same namespace that the IC is running. The CRD will define how the IC needs to be published on the BIGIP and what are the services that need to be configured; SSL Offloading, WAF, DDoS, HTTP Profiles, etc.

**Seperation.** 

Given the fact that we are sharing the same BIGIP device across all tenants, one of the most important things to consider how to allocate an IP address without creating conflict between the tenants. The way to achieve this is by using the F5 IPAM controller. The IPAM controller will be configured with different labels for each tenat and each label will contain the IP ranges assigned for the tenants.
These labels must be referenced on the CIS CRDs that will be used to publish the NGINX IC services.


More information on CIS and IPAM can be found on the following links:
- [CIS](https://clouddocs.f5.com/containers/latest/)
- [CIS CRDs](https://clouddocs.f5.com/containers/latest/userguide/crd/)
- [IPAM Controller](https://clouddocs.f5.com/containers/latest/userguide/ipam/)

## Demo 

