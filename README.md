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

## Navigating with Kubectl

Show nodes:

```
$ kubectl get nodes
NAME                                             STATUS   ROLES    AGE   VERSION
scw-k8s-mystifying-torvald-jovial-mclar-25a942   Ready    node     20h   v1.14.1
scw-k8s-mystifying-torvald-jovial-mclar-eaf1a2   Ready    node     20h   v1.14.1
scw-k8s-mystifying-torvalds-default-7f263aabab   Ready    master   20h   v1.14.1
```

Show services:

```
$ kubectl get services
NAME                    TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)           AGE
kubernetes              ClusterIP   10.32.0.1      <none>        443/TCP           20h
openfaas                ClusterIP   10.41.47.185   <none>        80/TCP            9h
python                  ClusterIP   10.42.56.141   <none>        80/TCP            9h
rancher                 ClusterIP   10.32.41.218   <none>        80/TCP            9h
```

Show Pods:

*To see pods from the kube-system namespace add -n kube-system*

```
$ kubectl get pods
NAME                                     READY   STATUS    RESTARTS   AGE
nginx-755464dd6c-28xnb                   1/1     Running   0          19h
nginx-755464dd6c-6h5xm                   1/1     Running   0          19h
nginx-755464dd6c-xg29p                   1/1     Running   0          19h
nginx2-7c55f68548-qcxsz                  1/1     Running   0          19h
openfaas-cffdddc4-lvn5w                  1/1     Running   0          9h
openfaas-cffdddc4-wbcl6                  1/1     Running   0          9h
python-65ccf9c74b-8kmgp                  1/1     Running   0          9h
python-65ccf9c74b-dgnqb                  1/1     Running   0          9h
rancher-597b6b8554-mgcjr                 1/1     Running   0          9h
rancher-597b6b8554-mpk62                 1/1     Running   0          9h
```

Show deployments:

```
$ kubectl get deployments -o wide
NAME                    READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS              IMAGES                      SELECTOR
nginx                   3/3     3            3           19h   nginx                   nginx                       run=nginx
nginx2                  1/1     1            1           19h   nginx                   nginx                       app=nginx2
openfaas                2/2     2            2           9h    logo                    ruanbekker/logos:openfaas   app=logo,task=openfaas
python                  2/2     2            2           9h    logo                    ruanbekker/logos:python     app=logo,task=python
rancher                 2/2     2            2           9h    logo                    ruanbekker/logos:rancher    app=logo,task=rancher
```

Show ingress:

```
$ kubectl get ingress -o wide
NAME      HOSTS                                                          ADDRESS   PORTS   AGE
logo      openfaas.domain.com,rancher.domain.com,python.domain.com       80      9h
```

Show system ingress:

```
$ kubectl get ingress -o wide -n kube-system
NAME             HOSTS                     ADDRESS   PORTS   AGE
traefik-web-ui   traefik-ui.domain.com               80      9h
```

## Access your Applications

Access the Traefik-UI, and filter for one of the applications. Let's take OpenFaaS for an example:

![image](https://user-images.githubusercontent.com/567298/59177432-63418080-8b5c-11e9-8e54-20600508e510.png)

Access the OpenFaaS Page via the URL:

![image](https://user-images.githubusercontent.com/567298/59177206-a4856080-8b5b-11e9-8954-238590f18e5c.png)


