## Kubernetes storage
- PVC
- PV
- Snapshot
- configMap
- Secret

### kubenetes volume
دیتا رو از داخل پاد میکشه بیرون و داخل یک والوم ذخیره میکنه

دیتا نباید داخل پاد باشد

اگر کانتینر از بین بره یه والیوم شیر باشه براشون جدیده میتونه از اون والیومه بخونه

### Host Path Storage
این ماژول خودش ریسک دارد و میتواند و نباید از آن به دلیل موارد امنیتی استفاده کرد چون هر کسی که به هاست دسترسی داشته باه میتونه سرتیفیکیت هامون رو برداره
      
ینی یه دایرکتوری از هاست رو مپ کنیم به یه دایرکتوری در پادمان


example ) 
```
apiVersion: v1
kind: Pod
metadata:
  name: test-host-path
spec:
  containers:
  - image: busybox:1.28
    name: test-host-path-container
    command: ['sh', '-c', 'sleep 3600']
    volumeMounts:
    - mountPath: /opt/
      name: test-volume
  volumes:
  - name: test-volume
    hostPath:
      # directory location on host
      path: /mnt/
      # this field is optional
      type: Directory
```      

test example ) :
```
**cehck node
kubectl get pod -o wide
NAME             READY   STATUS    RESTARTS   AGE   IP           NODE                 NOMINATED NODE   READINESS GATES
test-host-path   1/1     Running   1          95m   10.244.0.5   kind-control-plane   <none>           <none>
docker exec -it kind-control-plane /bin/bash
** create a directory on mnt
root@kind-control-plane:/mnt# mkdir /mnt/backup
** check mount from /mnt to /opt
kubectl exec -it pod/test-host-path   -- ls -lha /opt
```
### emptydir
مپ دایرکتوری خالی به پادمون - بعد از اینکه پادمون از بین میره دایرکتوریهم از بیم میره
دلیل : دایکرتوری برای یه بازه زمانی نیاز داشته که داخلش بنویشه، اصولا توی رم لود میکنند.


ٍExample):
```
Example of emptyDir – 1

apiVersion: v1
kind: Pod
metadata:
  name: test-pd
spec:
  containers:
  - image: k8s.gcr.io/test-webserver
    name: test-container
    volumeMounts:
    - mountPath: /cache
      name: cache-volume
  volumes:
  - name: cache-volume
    emptyDir: {}
```
