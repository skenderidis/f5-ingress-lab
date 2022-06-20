# PolicyCRD Examples

This section demonstrates the deployment of a Virtual Server with custom TCP, HTTP and WAF Profiles with PolicyCRD.

- [PolicyCRD XFF](#policycrd-xff)
- [PolicyCRD WAF](#policycrd-waf)

## PolicyCRD XFF
This section demonstrates the deployment of a Virtual Server with a custom HTTP Profiles that add XFF header.

Eg: policy-vs /policy-crd 
```yml
apiVersion: cis.f5.com/v1
kind: Policy
metadata:
  labels:
    f5cr: "true"
  name: policy-xff
spec:
  profiles:
    http: /Common/http-xff
---
apiVersion: cis.f5.com/v1
kind: VirtualServer
metadata:
  labels:
    f5cr: "true"
  name: xff-policy-vs
spec:
  virtualServerAddress: 10.1.10.97
  host: policy.f5demo.local
  policyName: policy-xff
  snat: auto
  pools:
    path: /
    service: echo-svc
    servicePort: 80
```

Create the PolicyCRD and VirtualServerCRD resources.
```
kubectl apply -f xff-policy.yml
kubectl apply -f vs-with-policy-xff.yml
```

Confirm that the VS CRD is deployed correctly. You should see `Ok` under the Status column for the VirtualServer that was just deployed.
```
kubectl get vs 
```

On the BIGIP we created a profile called `http-xff` that adds the client IP as an HTTP header (x-forwarded-for) before forading the transaction to the backend.

Access the service using the following example. 
```
curl -v http://policy.f5demo.local/ --resolve policy.f5demo.local:80:10.1.10.97
```

Verify that the `x-forwarded-for` Header exists and contains the client's actual IP



## PolicyCRD WAF
This section demonstrates the deployment of a Virtual Server with a WAF policy to protect against Layer 7 threats.

Eg: VirtualServer / PolicyCRD 
```yml
apiVersion: cis.f5.com/v1
kind: Policy
metadata:
  labels:
    f5cr: "true"
  name: waf-policy
spec:
  l7Policies:
    waf: /Common/basic_waf_policy
  profiles:
    http: /Common/http-xff
    logProfiles:
      - /Common/Log all requests
---
apiVersion: cis.f5.com/v1
kind: VirtualServer
metadata:
  labels:
    f5cr: "true"
  name: waf-policy-vs
spec:
  virtualServerAddress: 10.1.10.98
  host: waf.f5demo.local
  policyName: waf-policy
  snat: auto
  pools:
    path: /
    service: echo-svc
    servicePort: 80
```

Create the PolicyCRD and VirtualServerCRD resource.
```
kubectl apply -f waf-policy.yml
kubectl apply -f vs-with-policy-waf.yml
```

Confirm that the VS CRD is deployed correctly. You should see `Ok` under the Status column for the VirtualServer that was just deployed.
```
kubectl get vs 
```

On the BIGIP we created a WAF profile to block HTTP attacks, so we expect BIGIP to mitigate any L7 attack (according to the WAF policy) that is executed to the services running in K8S.

Access the service using the following example that contains a XSS violations. 
```
curl -v "http://waf.f5demo.local/index.php?parameter=<script/>" --resolve waf.f5demo.local:80:10.1.10.98
```

Verify that the  transaction that contains the attack gets blocked by BIGIP WAF.
```html
<html>
  <head>
    <title>Request Rejected</title>
  </head>
  <body>
    The requested URL was rejected. Please consult with your administrator.<br><br>
    Your support ID is: 4045204596866416688<br><br>
    <a href='javascript:history.back();'>[Go Back]</a>
  </body>
</html>
```

