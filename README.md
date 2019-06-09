# traefik-kubernetes-scaleway-demo
Traefik as an Ingress Controller on Kubernetes

## Provision a Kapsule Cluster

Have a look at the [provision a kapsule cluster on scaleway](#guides/README.md) guide if you have not provisioned the cluster.

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
