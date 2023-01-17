### DaemonSet
 A DaemonSet ensures that all (or some) Nodes run a copy of a Pod. As nodes are added to the cluster, Pods are added to them. As nodes are removed from the cluster, those Pods are garbage collected. Deleting a DaemonSet will clean up the Pods it created.

Some typical uses of a DaemonSet are:

running a cluster storage daemon on every node
running a logs collection daemon on every node
running a node monitoring daemon on every node

view daemonset
```
 kubectl get ds
NAME          DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
hello-world   1         1         1       1            1           <none>          44h
```

### Static pod VS Daemonsets

static pod
- create by kubelet (Skip api server)
- Deploy control plane

Damonset
- create by api server
- deploy login agent log agent monitor agent

daemon set add and check
```
kubectl apply -f https://github.com/milad-baousi/K8S/blob/main/daemonset.yaml
kubectl get ds
NAME          DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
hello-world   1         1         0       1            0           <none>          44h
```


