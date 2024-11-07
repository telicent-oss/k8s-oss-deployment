# Incoming PBAC filter

This step will create the resources required to deploy the PBAC filter on the
client side that checks the received messages before merging to the knowledge
topic.

## Prepare the filter component resources

### Filter environment file

[Source](../../workload/federation/4-incoming-filter/patches/config/config.env)

Update the Kafka bootstrap servers

## Review and apply resources to the cluster

Review the resources that will be created:

```
kubectl kustomize workload/federation/4-incoming-filter
```

Ensure you are logged into the client Kubernetes cluster. Create the federation
filter component:

```
kubectl --context <CLIENT CONTEXT> apply -k workload/federation/4-incoming-filter
```

## Check deployment status

Check the status of:

* Pod/Deployment/Replicaset

```
TODO
kubectl --context <CLIENT CONTEXT> get all -n tc-federation -l app.kubernetes.io/name=pbac-federation-filter
```
