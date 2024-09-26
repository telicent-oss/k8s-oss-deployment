# Pre-requisites

Deploying federation requires two instances of open source CORE. This section
provides further information on the pre-requisites detailed in:

[CORE deployment pre-requisites](../core/10-pre-requisites.md)

## CORE deployments

The example assumes that there are two physically separate deployments of open
source CORE and they have been deployed as per the CORE deployment
[example](../core/index.md).

## Istio ingress on the server CORE instance

Federation is a GRPC service. It should be hosted on a domain separate from the
application domain. This an example of providing that via an Istio ingress
proxy.

```
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: ingress-gateway
  namespace: istio-system
spec:
  selector:
    istio: ingress
  servers:
  - hosts:
    - federation.telicent.example.com
    port:
      name: http
      number: 80
      protocol: HTTP
    tls:
      httpsRedirect: true
  - hosts:
    - federation.telicent.example.com
    port:
      name: https
      number: 443
      protocol: HTTPS
    tls:
      # Name of config map that contains the TLS key and cert
      credentialName: federation-tls-key-and-cert
      mode: SIMPLE
  // Application host
  - hosts:
    - apps.telicent.example.com
    port:
      name: http
      number: 80
      protocol: HTTP
    tls:
      httpsRedirect: true
  - hosts:
    - apps.telicent.example.com
    port:
      name: https
      number: 443
      protocol: HTTPS
    tls:
      # Name of config map that contains the TLS key and cert
      credentialName: apps-tls-key-and-cert
      mode: SIMPLE
```

## Federation authentication

Federation is a service to service connection. The server authenticates clients
as part of the federation client server protocol. It should not be integrated
with the apps OIDC authentication flow.

## Redis

A Redis instance is required for the Federation client.
