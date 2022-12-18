## DNS
Kubernetes offers a DNS cluster addon Service that automatically assigns dns names to other Services. 
You can check if it's running on your cluster:
```
kubectl get services kube-dns --namespace=kube-system
NAME       TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)         AGE
kube-dns   ClusterIP   10.0.0.10    <none>        53/UDP,53/TCP   8m
```

## test DNS
```
kubectl run curl --image=radial/busyboxplus:curl -i --tty
```
Waiting for pod default/curl-131556218-9fnch to be running, status is Pending, pod ready: false
Hit enter for command prompt
Then, hit enter and run nslookup my-nginx:

```
[ root@curl-131556218-9fnch:/ ]$ nslookup my-nginx
Server:    10.0.0.10
Address 1: 10.0.0.10

Name:      my-nginx
Address 1: 10.0.162.149
```
refrences: https://kubernetes.io/
