
# Smart cache graph

This step will deploy Smart cache graph (SCG) and its associated configuration.
Unlike other services SCG is deployed via a StatefulSet.

## Prepare the Smart cache graph component resources

### Environment configuration file

[Source](../../workload/core/3-smart-cache-graph/server/patches/config/server.env)

Update the OIDC JWKS URL. The other configuration options should not be changed
in normal circumstances.

### Server configuration

[Source](../../workload/core/3-smart-cache-graph/server/patches/config/config.ttl)

Toward the end of the file (line 250 onwards) change the value of the two lines
of configuration that point to your Kafka cluster bootstrap server. The lines to
change look like this:

```
fk:bootstrapServers    "YOUR.KAFKA.BOOTSTRAP.SERVERS.COM:9092,YOUR.KAFKA.BOOTSTRAP.SERVERS.COM:9092";
```

### Block storage provider

[Source](../../workload/core/3-smart-cache-graph/kustomization.yaml)

In the SCG kustomization file update the inline patch for the block storage
provider for the target cluster.

### Update the kafka SASL password secret

[Source](../../workload/core/3-smart-cache-graph/server/patches/secret/kafka-config.properties)

Update the password.

## Review and apply resources to the cluster

Review the resources that will be created:

```
kubectl kustomize workload/core/3-smart-cache-graph
```

Create the SCG component:

```
kubectl apply -k workload/core/3-smart-cache-graph
```

## Check deployment status

Check the status of:

* Pod/StatefulSet/Service
* Virtual service
* AuthorizationPolicy

Note it can take a few minutes for the graph server to reach a ready state.

```
$ kubectl get all -n tc-graph
NAME                 READY   STATUS    RESTARTS   AGE
pod/graph-server-0   2/2     Running   0          3m32s

NAME                   TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE
service/graph-server   ClusterIP   10.43.236.40   <none>        3030/TCP,9464/TCP   5d10h

NAME                            READY   AGE
statefulset.apps/graph-server   1/1     5d10h

$ kubectl get virtualservices.networking.istio.io -n tc-graph
NAME                   GATEWAYS                           HOSTS                       AGE
graph-server-ingress   ["istio-system/ingress-gateway"]   ["apps.127.0.0.1.nip.io"]   5d10h

$ kubectl get authorizationpolicies.security.istio.io -n tc-graph
NAME                      ACTION   AGE
allow-ingress-to-server   ALLOW    14h
deny-by-default                    14h
```
