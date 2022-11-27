
### replica and deployment
POD < replica <  deployment 

### deployment use Case
- all stateless service
- to rollout replicaset
- clean up older Replicaset
- Scale up
 

### cause of deployment failed
- quata not exist
- Limit Ranges
- image pull error
- App misconfiguration

### update & downgrade Deployment
```
kubectl rollout history
kubectl rollout status
kubectl rollout undo
kubectl rollout pause
```

### Practice

replicaset.yaml
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: hello-world-rs
spec:
  replicas: 2
  selector:
    matchLabels:
      app: hello-world-rs
  template:
    metadata:
      labels:
        app: hello-world-rs
        svc: example
    spec:
      containers:
        - name: hello-world
          image: ahmadrafiee/go-hello-world:2
          ports:
            - containerPort: 80
          resources:
            requests:
              cpu: 10m
              memory: 10Mi
```
```
kubectl apply -f replicaset.yaml

kubectl get pod
hello-world-rs-fmjjk                     1/1     Running   0          5s
hello-world-rs-szxvr                     1/1     Running   0          5s

kubectl get rs
NAME             DESIRED   CURRENT   READY   AGE
hello-world-rs   2         2         2       2m18s

kubectl edit rs hello-world-rs --> change replica count
replicas = 5
```
### create a new Deployment
01-Deployment.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-world
  annotations:
    kubernetes.io/change-cause: ver1
spec:
  revisionHistoryLimit: 3
  selector:
    matchLabels:
      app: hello-world-deploy
  replicas: 2
  template:
    metadata:
      labels:
        app: hello-world-deploy
        svc: example
    spec:
      containers:
        - name: hello-world
          image: ahmadrafiee/go-hello-world:1
          ports:
            - containerPort: 80
          resources:
            requests:
              cpu: 10m
              memory: 10Mi
            limits:
              memory: 20Mi
              cpu: 20m
              ```
```
```
kubectl apply -f 01-deployment.yaml

kubectl get deployment
NAME          READY   UP-TO-DATE   AVAILABLE   AGE
hello-world   0/2     2            0           11s

kubectl get pod -A
default                hello-world-8977c54c9-fq5gt                   1/1     Running   0              13m
default                hello-world-8977c54c9-hqsst                   1/1     Running   0              13m
8977c54c9 = id replicaset under of deployment
```

edit deplyment
```
kubectl edit deployments.apps hello-world
```

rollout Deployment
```
kubectl rollout history deployment hello-world
deployment.apps/hello-world
REVISION  CHANGE-CAUSE
2         ver1
3         ver1

kubectl rollout undo deployment hello-world
deployment.apps/hello-world rolled back

#other revsion
kubectl rollout undo deployment/nginx-deployment --to-revision=2
```
