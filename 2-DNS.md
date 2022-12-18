## DNS
Kubernetes offers a DNS cluster addon Service that automatically assigns dns names to other Services. 
You can check if it's running on your cluster:
```
kubectl get services kube-dns --namespace=kube-system
NAME       TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)         AGE
kube-dns   ClusterIP   10.0.0.10    <none>        53/UDP,53/TCP   8m
```

refrences: https://kubernetes.io/
