### k8s service label and selector.md
در صورتی که بخواهیم name space امان را به پادهایمان متصل کنیم از راهکار زیر استفاده میکنیم.
با این روش میتوانیم برای کلاسترمان پاد ها را بصورت ایزوله در نظر بگیریم



همان طور که در شکل مشاهده میکنیم

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx01
  labels:
    app.kubernetes.io/name: proxy
spec:
  containers:
  - name: nginx
    image: nginx:stable
    ports:
      - containerPort: 80
        name: http-web-svc

apiVersion: v1
kind: Pod
metadata:
  name: nginx02
  labels:
    app.kubernetes.io/name: proxy
spec:
  containers:
  - name: nginx
    image: nginx:stable
    ports:
      - containerPort: 80
        name: http-web-svc
------------------------------------
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app.kubernetes.io/name: proxy
  ports:
  - name: name-of-service-port
    protocol: TCP
    port: 80
    targetPort: http-web-svc
```
دقت شود سرویس از طریق Selector  و pod از طریق labels که مشابه باشند به هم متصل میشوند.

```
kubectl get svc
NAME            TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
nginx-service   ClusterIP   10.96.50.212   <none>        80/TCP    9m26s
```
	
### End Point


اند پوینت پاد های زیر مجموعه اون سرویس رو به ما نشون میده. 
	
```
kubectl get ep
NAME            ENDPOINTS                       AGE
kubernetes      172.18.0.2:6443                 8d
nginx-service   10.244.0.22:80,10.244.0.23:80   13m
```
	
