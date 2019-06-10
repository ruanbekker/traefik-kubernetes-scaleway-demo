## Deploy a Kapsule Cluster on Scaleway

Head over to Kapsule and provision a Kubernetes Cluster

![image](https://user-images.githubusercontent.com/567298/59164353-e71f4c80-8b0b-11e9-8f5c-7c65db1af7b2.png)

After the cluster has been provisioned, you will get information about your endpoints from the Cluster Infromation Section, which we will need for our ingresses:

![image](https://user-images.githubusercontent.com/567298/59180685-df8c9180-8b65-11e9-82aa-05ee3cd42c78.png)

Scroll down to download your config:

![image](https://user-images.githubusercontent.com/567298/59164356-f56d6880-8b0b-11e9-8c00-34dff0ba61fb.png)

Move your config in place:

```
$ mv ~/Downloads/kubeconfig-k8s-mystifying-torvalds.yaml ~/.kube/config
```

## Test it

Test the connection by getting the info of your nodes:

```
$ kubectl get node
NAME                                             STATUS    ROLES     AGE       VERSION
scw-k8s-mystifying-torvalds-default-7f263aabab   Ready     <none>    4m        v1.14.1
```

## Add more nodes

Provision another pool with more nodes which will act as our nodes in our cluster:

![image](https://user-images.githubusercontent.com/567298/59164387-4e3d0100-8b0c-11e9-8633-b3fc680ac4cd.png)

After the pool has been provisioned, verified that they have joined the cluster:

```
$ kubectl get nodes
NAME                                             STATUS    ROLES     AGE       VERSION
scw-k8s-mystifying-torvald-jovial-mclar-25a942   Ready     <none>    2m        v1.14.1
scw-k8s-mystifying-torvald-jovial-mclar-eaf1a2   Ready     <none>    2m        v1.14.1
scw-k8s-mystifying-torvalds-default-7f263aabab   Ready     <none>    15m       v1.14.1
```

## Label the nodes

Label the nodes as following:

```
$ kubectl label node scw-k8s-mystifying-torvalds-default-7f263aabab node-role.kubernetes.io/master=""
node "scw-k8s-mystifying-torvalds-default-7f263aabab" labeled

$ kubectl label node scw-k8s-mystifying-torvald-jovial-mclar-eaf1a2 node-role.kubernetes.io/node=""
node "scw-k8s-mystifying-torvald-jovial-mclar-eaf1a2" labeled

$ kubectl label node scw-k8s-mystifying-torvald-jovial-mclar-25a942 node-role.kubernetes.io/node=""
node "scw-k8s-mystifying-torvald-jovial-mclar-25a942" labeled
```

Now when we inspect the nodes, they will be labeled:

```
$ kubectl get nodes
NAME                                             STATUS    ROLES     AGE       VERSION
scw-k8s-mystifying-torvald-jovial-mclar-25a942   Ready     node      5m        v1.14.1
scw-k8s-mystifying-torvald-jovial-mclar-eaf1a2   Ready     node      5m        v1.14.1
scw-k8s-mystifying-torvalds-default-7f263aabab   Ready     master    17m       v1.14.1
```

