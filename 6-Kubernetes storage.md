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

      
