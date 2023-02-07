# kubernetes probes
 چرا پراب وجود داره :  وقتی مثلا یه کانتینر تازه میاد بالا اگر پراب وجود نداشت فورا سمتش درخواست میرفت و این باعث میشه ما کلی پکت رو از دست بدیم چون ممکنه
 زمان استارت شدن سرویس طول بکشه
 
 
 ## 3 type handler داریم
 1- exec action
 
یه کامند داخل کانتیتر اجرا میشه و  در صورت اجرا کد صفر برگرداند
 
 2-Tcp Socket Action
 
 
پورت سرویس رو چک میکنه که باز باشه
 
3- HTTP Get ACtion

 کامند http رو get میکنه اکر 200 برگردونه سرویس باز هست
 
 
 ## 3 type of probes on kubernetes
 
1- Startup probe

استارت پروسه زمان زیادی میبرد که باید جای liveness استفاده کنیم.  


2-liveness probe

زمانی که طول میکشه که اپلیکیشن ما داخل پاد زنده بشه، زنده شدن به معنای اینکه ترافیک سمتش بره نیست و ممکنه برای آمادگی زمان بیشتری بخاد

3- readiness

زمانی که طول میکشه اپلیکیشن ما آماده بشه که سرویس بده 

### Example liveness
```
kubectl edit pod -n kube-system kube-apiserver-kind-control-plane

got to api server config and view this lines

 livenessProbe:
      failureThreshold: 8
      httpGet:
        host: 172.18.0.3
        path: /livez
        port: 6443
        scheme: HTTPS
        
 readinessProbe:
      failureThreshold: 3
      httpGet:
        host: 172.18.0.3
        path: /readyz
        port: 6443
        scheme: HTTPS
      periodSeconds: 1
      successThreshold: 1
      timeoutSeconds: 15
       
        
این چند خط به ما میگه اگر میخایم چک کنیم ای پی ای زنده است یا نه باید با این آیپی و پورت بررسی کنیم

```

### Practice ) :

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  namespace: my-namespace
  labels:
    app: my-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-app
          image: nginx
          ports:
            - containerPort: 80
            - containerPort: 2222
          livenessProbe:
            httpGet:
              path: /healthz
              port: 2222
          readinessProbe:
            httpGet:
              path: /healthz
              port: 2222
```
#### check liveness readyness
```
kubectl describe pod nginx-85d69968d9-j6vlg -n kube-system
 
Liveness:       http-get http://:2222/healthz delay=0s timeout=1s period=10s #success=1 #failure=3
Readiness:      http-get http://:2222/healthz delay=0s timeout=1s period=10s #success=1 #failure=3
```


