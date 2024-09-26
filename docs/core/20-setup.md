# Set up

## Gather relevant configuration values:

| Value | Notes |
|-|-|
| Hostname | The domain Telicent will be served from |
| OIDC Issuer URL |  |
| OIDC JWKS URL | |
| Mongo database protocol | e.g. `mongodb` or `mongodb+srv`|
| Mongo database connection string | |
| Mongo database username | For the Access API |
| Mongo database password | For the Access API |
| Block storage class name | | 

## Directory structure

The example deployment uses relative paths to the Telicent manifests. The
manifests are distributed as a tarball. Untar the manifests in a different
location and symlink to the `telicent` directory of the Kubernetes manifests.
E.G:

```
$ ll -l
total 48
-rw-r--r--@ 1 staff  staff   3.5K 18 Sep 14:56 README.md
lrwxr-xr-x@ 1 staff  staff    22B 18 Sep 14:04 telicent -> k8s-manifests/telicent
drwxr-xr-x@ 8 staff  staff   256B 18 Sep 14:08 workload
```

The `workload/core` directory contains overlays for various elements of the Telicent
deployment. It also contains shared components to reduce boilerplate and
repetition.

```
tree -L 1 workload/core
workload/core
├── 1-namespaces
├── 2-access
├── 3-smart-cache-graph
├── 4-query
└── components
```

## Configure components

The `workload/core/components` directory contains these patches that should be
configured to tailor the deployment to the target environment.

```
tree -L 2 workload/core/components/patches
workload/core/components/patches
├── authorizationpolicy
│   └── serviceaccount
└── virtualservice
    └── host
```
### Virtual service host

[Source](../../workload/core/components/patches/virtualservice/host/apps/patch/host.yaml)

This patch updates host within all virtual services. Change the hostname to the
required value for this environment.

### Authorization policy service account (Optional)

[Source](../../workload/core/components/patches/authorizationpolicy/serviceaccount/kustomization.yaml)

This patch updates the principal of the ingress proxy in AuthorizationPolicies
if it differs from the assumed value of:

```
cluster.local/ns/istio-system/sa/istio-ingress`
```

Update the name and include this patch if required.
