# Federation server

This step will create the resources required to deploy the federation server in
the source CORE instance.

## Prepare the Federation server component resources

### Server configuration file

[Source](../../workload/federation/1-server/patches/config/server.properties)

Update the Kafka bootstrap servers

### Configure the access control file

[Source](../../workload/federation/1-server/patches/config/access.json)

Update the client password hash and salt with the values generated earlier.

### Configure the virtual service host

[Source](../../workload/federation/1-server/patches/virtualservice/host/federation/patch/host.yaml)

Configure the federation host name.

## Review and apply resources to the cluster

Review the resources that will be created:

```
kubectl kustomize workload/federation/1-server
```

Ensure you are logged into the server Kubernetes cluster. Create the federation
server component:

```
kubectl --context <SERVER CONTEXT> apply -k workload/federation/1-server
```

## Check deployment status

Check the status of:

* Pod/Deployment/Replicaset
* Service
* Virtual service
* AuthorizationPolicies

```
$ kubectl --context <SERVER CONTEXT> get all -n tc-federation
NAME                                    READY   STATUS    RESTARTS   AGE
pod/federator-server-558dd7f8d6-55sch   2/2     Running   0          4m32s

NAME                       TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/federator-server   ClusterIP   10.100.188.80   <none>        8080/TCP   8m6s

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/federator-server   1/1     1            1           8m6s

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/federator-server-558dd7f8d6   1         1         1       4m32s
replicaset.apps/federator-server-7c8478bb4d   0         0         0       8m6s

$ kubectl --context <SERVER CONTEXT> get virtualservices.networking.istio.io -n tc-federation
NAME                        GATEWAYS                           HOSTS                                       AGE
federation-server-ingress   ["istio-system/ingress-gateway"]   ["federation.server.telicent.example.com"]  8m55s

$ kubectl --context <SERVER CONTEXT> get authorizationpolicies.security.istio.io -n tc-federation
NAME                                 AGE
allow-ingress-to-federation-server   10m
deny-by-default                      2d19h
```
