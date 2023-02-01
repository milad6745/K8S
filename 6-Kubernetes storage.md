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
## PV & PVC (persistent volume - persistent volume claim)
سرویس PVC سمت کاربر و PV سمت ادمین است.
پی وی PV به ما استوریج میدهد .

![Storageclass blog (1)-1](https://user-images.githubusercontent.com/113288076/215782708-8988d9b1-9a17-41b0-968f-b031c7330fd8.png)

ایراد : نیاز به ادمین دارد . PV رو ایجاد کنه بر اون اساس داخل ریموت استوریج ساخته بشه.
این فلو منوال هست و در کلاستر های بزرگ ادمین باید صد ها PV ایجاد کنه .
در نهایت PV و PVC های متقارن با هم باند میشن .
### storage class : 
ادمین استوریج کلاس درست میکنه،  دسترسی رو استور میکنند و کاربران میتوندPVC بسازند اما نمیتونن بازش کنند .   این فلو حالا داینامیک میشه .  dynamic provisioning
من یه PVC میسازم با استوریج کلاس اتوماتیک PV و والوم ساخته میشوند.


** check storage class and pv and pvc **
```
kubectl get sc
NAME                 PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
standard (default)   rancher.io/local-path   Delete          WaitForFirstConsumer   false                  25h

get pv
No resources found
root@milad-ubuntu:/mnt# kubectl get pvc
No resources found in default namespace
```
example ):
```
apiVersion: v1
kind: Service
metadata:
  name: wordpress-mysql
  labels:
    app: wordpress
spec:
  ports:
    - port: 3306
  selector:
    app: wordpress
    tier: mysql
  clusterIP: None
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
  labels:
    app: wordpress
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 20Gi
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress-mysql
  labels:
    app: wordpress
spec:
  selector:
    matchLabels:
      app: wordpress
      tier: mysql
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: wordpress
        tier: mysql
    spec:
      containers:
      - image: mysql:5.6
        name: mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-pass
              key: password
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
```
check example
```
kubectl get pvc
NAME             STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
mysql-pv-claim   Bound    pvc-379a333e-e115-450a-8fa8-c0f74a5d40d6   20Gi       RWO            standard       13s
root@milad-ubuntu:/home/milad/kuber# kubectl get pv
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                    STORAGECLASS   REASON   AGE
pvc-379a333e-e115-450a-8fa8-c0f74a5d40d6   20Gi       RWO            Delete           Bound    default/mysql-pv-claim   standard                5s
root@milad-ubuntu:/home/milad/kuber# kubectl get service
NAME              TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
kubernetes        ClusterIP   10.96.0.1    <none>        443/TCP    25h
wordpress-mysql   ClusterIP   None         <none>        3306/TCP   20s
```

با این روش یه PV بصورت اتوماتیک ساخته شد، به واسطه استوریج کلاس

** dynamic provision and static provision **

مدل استاتیک : درخواست برای ساخت PV ارسال میشه بعد PVC ساخته میشه و به هم باند میشن
مدل داینامیک : PVC ساهخته میشه و بر اسا اون PV ساخته میشه و به هم باند میشن



## Access Modes
- read write once :  
یه والیومیه که  یه نود میتونه روش بخونه و بنویسه ، جنرال ترین مدل هست
- read only many :
یه والیومیه که پاد ها میتونن ازش بخونن
- read write Many
-یه والیومیه که قابلیت خواندن نوشتن داره
- read wirte once
فقط از یه نود میشه روش خواند و نوشت
![accessmod](https://user-images.githubusercontent.com/113288076/216014017-d194f579-34e7-4716-8650-8701671a8d01.PNG)


### reclaim policy 
وقتی PV پاک میشه چه مدلی باشه :
- retain : دستی Reclam
- recycle
- delete : default

