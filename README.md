# traefik-kubernetes-scaleway-demo
Traefik as an Ingress Controller on Kubernetes

## Provision a Kapsule Cluster

Have a look at the [provision a kapsule cluster on scaleway](guide/README.md) guide if you have not provisioned the cluster.

## Provision Traefik as an Ingress Controller

```
$ kubectl apply -f traefik/01-traefik-rbac.yaml
clusterrole.rbac.authorization.k8s.io/traefik-ingress-controller created
clusterrolebinding.rbac.authorization.k8s.io/traefik-ingress-controller created
```
```
$ kubectl apply -f traefik/02-traefik-deployment.yaml
serviceaccount/traefik-ingress-controller created
deployment.extensions/traefik-ingress-controller created
service/traefik-ingress-service created
```
```
$ kubectl apply -f traefik/03-traefik-ds.yaml
serviceaccount/traefik-ingress-controller unchanged
daemonset.extensions/traefik-ingress-controller created
The Service "traefik-ingress-service" is invalid:
* spec.ports[0].nodePort: Forbidden: may not be used when `type` is 'ClusterIP'
* spec.ports[1].nodePort: Forbidden: may not be used when `type` is 'ClusterIP'
```
```
$ kubectl replace -f traefik/03-traefik-ds.yaml
serviceaccount/traefik-ingress-controller replaced
daemonset.extensions/traefik-ingress-controller replaced
The Service "traefik-ingress-service" is invalid: spec.clusterIP: Invalid value: "": field is immutable
```
```
$ kubectl delete -f traefik/03-traefik-ds.yaml
serviceaccount "traefik-ingress-controller" deleted
daemonset.extensions "traefik-ingress-controller" deleted
service "traefik-ingress-service" deleted
```
```
$ kubectl apply -f traefik/03-traefik-ds.yaml
serviceaccount/traefik-ingress-controller created
kubectl create deploy nginx2 --image nginx
daemonset.extensions/traefik-ingress-controller created
service/traefik-ingress-service created
```
```
$ kubectl apply -f traefik/04-traefik-ui.yaml
service/traefik-web-ui created
```
```
$ kubectl apply -f traefik/05-traefik-ui-ingress.yaml
ingress.extensions/traefik-web-ui created
```

```
$ kubectl get services --namespace=kube-system
NAME                      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                  AGE
coredns                   ClusterIP   x.x.x.x         <none>        53/UDP,53/TCP,9153/TCP   11h
heapster                  ClusterIP   x.x.x.x         <none>        80/TCP                   11h
kubernetes-dashboard      ClusterIP   x.x.x.x         <none>        443/TCP                  11h
metrics-server            ClusterIP   x.x.x.x         <none>        443/TCP                  11h
monitoring-influxdb       ClusterIP   x.x.x.x         <none>        8086/TCP                 11h
traefik-ingress-service   ClusterIP   x.x.x.x         <none>        80/TCP,8080/TCP          24m
traefik-web-ui            ClusterIP   x.x.x.x         <none>        80/TCP                   24m
```

## Deploy the Logo App to the Cluster

We will deploy the logo app to our cluster:

```
$ kubectl apply -f logos-app/logos-services.yaml
service/openfaas created
service/rancher created
service/python created
```

```
$ kubectl apply -f logos-app/logos-deployments.yaml
deployment.extensions/openfaas created
deployment.extensions/rancher created
deployment.extensions/python created
```

```
$ kubectl apply -f logos-app/logos-ingress.yaml
ingress.extensions/logo created
```

```
$ kubectl get pods
NAME                                     READY   STATUS    RESTARTS   AGE
openfaas-cffdddc4-lvn5w                  1/1     Running   0          4m6s
openfaas-cffdddc4-wbcl6                  1/1     Running   0          4m6s
python-65ccf9c74b-8kmgp                  1/1     Running   0          4m6s
python-65ccf9c74b-dgnqb                  1/1     Running   0          4m6s
rancher-597b6b8554-mgcjr                 1/1     Running   0          4m6s
rancher-597b6b8554-mpk62                 1/1     Running   0          4m6s
```
