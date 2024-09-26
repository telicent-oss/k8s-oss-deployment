# Access

This step will create the resources required to deploy the Access API and UI.
The manifests are installed in multiple namespaces concurrently.

## Prepare the Access component resources

### API configuration file

[Source](../../workload/core/2-access/api/patches/config/api.env)

Update the hostname, mongodb protocol, mongodb URL, mongodb username and OIDC provider URL.

### UI configuration file

[Source](../../workload/core/2-access/ui/patches/config/env-config.js)

Update the hostname

### MongoDB password secret

[Source](../../workload/core/2-access/kustomization.yaml)

Update the mongodb password.

## Review and apply resources to the cluster

Review the resources that will be created:

```
kubectl kustomize workload/core/2-access
```

Create the Access component:

```
kubectl apply -k workload/core/2-access
```

## Check deployment status

### Access API:

Check the status of:

* Pod/Deployment/Replicaset
* Service
* Virtual service
* AuthorizationPolicies

```
$ kubectl get all -n tc-system
NAME                              READY   STATUS    RESTARTS       AGE
pod/access-api-6d55c97bd4-dgzmv   2/2     Running   1 (5d5h ago)   5d5h

NAME                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
service/access-api   ClusterIP   10.43.155.76   <none>        8080/TCP   5d5h

NAME                         READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/access-api   1/1     1            1           5d5h

NAME                                    DESIRED   CURRENT   READY   AGE
replicaset.apps/access-api-6d55c97bd4   1         1         1       5d5h

$ kubectl get virtualservices.networking.istio.io -n tc-system
NAME                 GATEWAYS                           HOSTS                       AGE
access               ["istio-system/ingress-gateway"]   ["apps.127.0.0.1.nip.io"]   5d5h

$ kubectl get authorizationpolicies.security.istio.io -n tc-system
NAME                                     ACTION   AGE
allow-core-apps-to-api                   ALLOW    13h
allow-ingress-to-api                     ALLOW    13h
deny-by-default                                   13h
```

### Access UI:

Check the status of:

* Pod/Deployment/Replicaset
* Service
* VirtualService
* AuthorizationPolicy

```
kubectl get all -n tc-ui
NAME                             READY   STATUS    RESTARTS   AGE
pod/access-ui-79b9586668-ppr77   2/2     Running   0          5d5h

NAME                TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
service/access-ui   ClusterIP   10.43.231.154   <none>        80/TCP    5d5h

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/access-ui   1/1     1            1           5d5h

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/access-ui-79b9586668   1         1         1       5d5h

$ kubectl get virtualservices.networking.istio.io -n tc-ui
NAME                GATEWAYS                           HOSTS                       AGE
access-ui-ingress   ["istio-system/ingress-gateway"]   ["apps.127.0.0.1.nip.io"]   5d5h

$ kubectl get authorizationpolicies.security.istio.io -n tc-ui
NAME                              ACTION   AGE
allow-ingress-to-access-ui        ALLOW    14h
deny-by-default                            14h
```
