
# Query UI

This step installs the Query UI which is the final component to be installed.

## Prepare the Query UI component resources

### Environment configuration

[Source](../../workload/core/4-query/ui/patches/config/env-config.js)

Update the host value in the configuration file

## Review and apply resources to the cluster

Review the resources that will be created:

```
kubectl kustomize workload/core/4-query
```

Create the Query UI component:

```
kubectl apply -k workload/core/4-query
```

## Check deployment status

Check the status of:

* Pod/Deployment/Replicaset
* Service
* Virtual service

```
$ kubectl get all -n tc-ui
NAME                           READY   STATUS    RESTARTS   AGE
pod/query-ui-8bbd4fb55-w8sd8   2/2     Running   0          26m

NAME               TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
service/query-ui   ClusterIP   10.43.131.43   <none>        80/TCP    5d10h

NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/query-ui   1/1     1            1           5d10h

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/query-ui-8bbd4fb55   1         1         1       5d10h

$ kubectl get virtualservices.networking.istio.io -n tc-ui
NAME                     GATEWAYS                           HOSTS                       AGE
query-ui-ingress         ["istio-system/ingress-gateway"]   ["apps.127.0.0.1.nip.io"]   5d10h

$ kubectl get authorizationpolicies.security.istio.io -n tc-ui
NAME                              ACTION   AGE
allow-ingress-to-query-ui         ALLOW    14h
deny-by-default                            14h
```
