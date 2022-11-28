### state full set
 apply statefulset 
```
kubectl apply -f https://raw.githubusercontent.com/milad-baousi/K8S/main/StatefulSet.yaml
 ```
 
 edit my state fulset
 ```
 kubectl edit statefulsets.apps hello-world --> change to 3 replica
 and replica 3,4 deleted from end of list
 ```
 
 view pods
 when edit my statefullset Changes are made from the last pod

 ```
 kubectl get pod -A
 NAMESPACE     NAME                                          READY   STATUS    RESTARTS     AGE
default       hello-world-0                                 1/1     Running   0            42h
default       hello-world-1                                 1/1     Running   0            42h
default       hello-world-vm5dl                             1/1     Running   0            42h
```

