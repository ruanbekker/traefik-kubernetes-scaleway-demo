# traefik-kubernetes-scaleway-demo
Traefik as an Ingress Controller on Kubernetes

## Provision a Kapsule Cluster

Have a look at the [provision a kapsule cluster on scaleway](#guides/README.md) guide if you have not provisioned the cluster.

## Provision Traefik as an Ingress Controller

```
kubectl apply -f traefik/01-traefik-rbac.yaml
kubectl apply -f traefik/02-traefik-deployment.yaml
kubectl apply -f traefik/03-traefik-ds.yaml
kubectl replace -f traefik/traefik-ds.yaml
kubectl replace -f traefik/03-traefik-ds.yaml
kubectl delete -f traefik/03-traefik-ds.yaml
kubectl apply -f traefik/03-traefik-ds.yaml
kubectl apply -f traefik/04-traefik-ui.yaml
```
