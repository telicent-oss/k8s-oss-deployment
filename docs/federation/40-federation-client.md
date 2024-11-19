# Federation server

This step will create the resources required to deploy the federation client in
the destination CORE instance.

## Prepare the Federation client component resources

### Client configuration file

[Source](../../workload/federation/2-client/patches/config/client.properties)

Update the Kafka bootstrap servers, Redis configuration and federation server
host configuration.

### Configure the client password

[Source](../../workload/federation/2-client/patches/config/client-secret.env)

Update the client password with the value generated earlier.

### Update the kafka SASL password secret

[Source](../../workload/federation/2-client/patches/secret/kafka-config.properties)

Update the password.

## Review and apply resources to the cluster

Review the resources that will be created:

```
kubectl kustomize workload/federation/2-client
```

Ensure you are logged into the client Kubernetes cluster. Create the federation
client component:

```
kubectl --context <CLIENT CONTEXT> apply -k workload/federation/2-client
```

## Check deployment status

Check the status of:

* Pod/Deployment/Replicaset
* Service
* Virtual service
* AuthorizationPolicies

```
$ kubectl --context <CLIENT CONTEXT> get all -n tc-federation
NAME                                    READY   STATUS    RESTARTS   AGE
pod/federator-server-558dd7f8d6-55sch   2/2     Running   0          4m32s

NAME                       TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/federator-server   ClusterIP   10.100.188.80   <none>        8080/TCP   8m6s

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/federator-server   1/1     1            1           8m6s

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/federator-server-558dd7f8d6   1         1         1       4m32s
replicaset.apps/federator-server-7c8478bb4d   0         0         0       8m6s

$ kubectl --context <CLIENT CONTEXT> get virtualservices.networking.istio.io -n tc-federation
NAME                        GATEWAYS                           HOSTS                                       AGE
federation-server-ingress   ["istio-system/ingress-gateway"]   ["federation.server.telicent.example.com"]  8m55s

$ kubectl --context <CLIENT CONTEXT> get authorizationpolicies.security.istio.io -n tc-federation
NAME                                 AGE
allow-ingress-to-federation-server   10m
deny-by-default                      2d19h
```
