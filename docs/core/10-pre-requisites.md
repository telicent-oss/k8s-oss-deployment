# Pre-requisites

This section covers information on the pre-requisites for deploying Telicent
open source components. The dependencies are:

* Kubernetes cluster
* Istio service mesh installed and integrated with an OIDC provider
* Block storage provider for Kubernetes
* Kafka cluster
* MongoDB
* Tooling

Deployment of these components is infrastructure provider specific and outside
the scope of this example.

## Istio

Important aspects of the Istio integration are covered in this section. Istio
integration examples are provided as information only and provided to help
integrators plan their own deployment dependencies.

### Ingress

Telicent components need to be hosted on their domain. The domain should be
listening on a the HTTPS port (443) and present a valid TLS certificate.

This is an example of providing that via an Istio ingress proxy.

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

### OIDC Authentication

Istio should be integrated with an OIDC conformant Identity Provider (IdP) e.g.
Keycloak, Cognito etc.

We require that authentication is performed by the service mesh using an OIDC
authentication flow. All paths exposed on the Telicent domain should be
authenticated.

This is an example of an authorisation policy applied to an Istio ingress proxy
that triggers a custom authorisation provider to initiate the OIDC flow:

```
apiVersion: security.istio.io/v1beta1
kind: AuthorizationPolicy
metadata:
  name: authenticate-apps
  namespace: istio-system
spec:
  action: CUSTOM
  provider:
    name: oauth2-proxy-apps
  rules:
  - to:
    - operation:
        hosts:
        - apps.telicent.example.com
        - apps.telicent.example.com:*
        paths:
        - /*
        - /
  selector:
    matchLabels:
      istio: ingress
```

### OIDC Token

The OIDC token that describes the result of a successful authentication is
passed in a HTTP header upstream from the Istio ingress upstream to the service
and any subsequent service to service calls. The OIDC Token is a [JSON web
token](https://jwt.io/) that represents the result of OIDC authentication flow.
It passed between services as a HTTP header. The format is: 

```
Authorization: Bearer <BASE 64 ENCODED JWT>
```

Note use of this header is a de-facto standard and the key (`Authorization`) and
prefix (`Bearer `) are both configurable in Istio and the services.

The contents of the token payload look like this:

```
{
  "iss": "https://auth.devops.telicent-sandbox.telicent.live",
  "sub": "CiQ2NmUyNjJjNC01MDUxLTcwNDItNDI0NC04MWI5ODQzODljNGMSB2NvZ25pdG8",
  "aud": "ATPbmgBCBgSunk2B",
  "exp": 1718289028,
  "iat": 1718288728,
  "at_hash": "GXAwdA1efq5sxkK16-YJvA",
  "c_hash": "9HhhqsDtIGEK-xsUNLuFCA",
  "email": "admin+devops.admin@telicent.io",
  "email_verified": true,
  "groups": [
    "tc_read",
    "tc_admin",
    "tc_write"
  ]
}
```

The claims are OIDC specific and will be configured via the identity provider.
The claims that Telicent depend on are:

* `email`: to identify the user uniquely
    * NB there is an assumption that the IdP enforces a 1 to 1 mapping of users
      to emails
* `groups`: these are used in authorisation policies that control access to the
  Telicent resources.
    * If the IdP cannot generate the `groups` claim or its contents exactly then
      it is possible to patch the authorisation policies to reflect the precise
      values. However this should approached carefully as it has security
      implications.

### Istio Authorization policy principals

Authorization policies are implemented to restrict communications between
components. These principals are based on the namespace a service is deployed to
and the service account it runs as.

In particular, the principal that the Istio ingress is assigned is environment
specific and may differ from the one specified in the ingress to service
authorization policies. If this is the case then the authorization policies
should be patched to match the correct Istio ingress identity.

## Block storage provider

We require that smart cache graph persistent volumes are backed by block storage
for performance. Ensure there is an appropriate storage provider and storage
class configured on the cluster.

## Kafka

### Authentication

Kafka authentication is configured by passing in a location  of a file that
contains the standard Kafka SASL configuration options. That is passed into the
underlying libraries.

Kafka is assumed to be deployed with SSL/SCRAM authentication configured. Other
configurations may work (e.g. PLAIN_TEXT/SCRAM) but haven't been tested.

### Topics

These Kafka topics should be pre-created with a single partion:

* `knowledge`
* `ontology`

Note: single partitions are required to ensure deterministic message ordering
during ingest.

## MongoDB

Telicent Access requires a MongoDB database. MongoDB should be configured with a
user who has read/write permissions on the database `access`.

## Tooling

The only tooling required for deploying this example is a version of `kubectl`
compatible with the target Kubernetes cluster.

We have found these tools useful when working with Kubernetes and Kustomize.

* K9S for cluster overview
* A good visual diff tool e.g. BeyondCompare
* The `kustomize` CLI 

## Kustomize

The deployment of Telicent OSS is implemented with Kustomize. A working
knowledge of Kustomize and familiarity with structuring Kubernetes manifests for
Kustomize is assumed. In particular we make use of:

* Kustomization
* Components
* Configmap generators

And for this example:

* Secret generators

Note we do not recommend Secret generators for production environments. Secrets
management is outside the scope of our deployment manifests.
