# Create namespaces

This step will create the namespaces required for all other components. For each
namespace the manifests:

* Add annotations to enable Istio
* Create a default deny all AuthorizationPolicy
* Configure strict peer authentication with a PeerAuthentication resource

## Prepare the workload/core resources

There is no need to tailor these resources to the environment. 

## Review and apply resources to the cluster

Review the resources that will be created:

```
kubectl kustomize workload/core/1-namespaces
```

Create the namespaces:

```
kubectl apply -k workload/core/1-namespaces
```
