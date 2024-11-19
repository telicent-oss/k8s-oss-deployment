# Outgoing PBAC filter

This step will create the resources required to deploy the PBAC filter on the
server side that selects the knowledge messages to be sent to the client.

NB the filter reuses the Kafka authentication configuration secret created in
the server deployment.

## Prepare the filter component resources

### Filter environment file

[Source](../../workload/federation/3-outgoing-filter/patches/config/config.env)

Update the Kafka bootstrap servers

## Review and apply resources to the cluster

Review the resources that will be created:

```
kubectl kustomize workload/federation/3-outgoing-filter
```

Ensure you are logged into the server Kubernetes cluster. Create the federation
filter component:

```
kubectl --context <SERVER CONTEXT> apply -k workload/federation/3-outgoing-filter
```

## Check deployment status

Check the status of:

* Pod/Deployment/Replicaset

```
kubectl --context <SERVER CONTEXT> get all -n tc-federation -l app.kubernetes.io/name=pbac-federation-filter
```
