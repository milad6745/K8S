## Horizontal Pod Autoscaling (HPA)
In Kubernetes, a HorizontalPodAutoscaler automatically updates a workload resource
(such as a Deployment or StatefulSet), with the aim of automatically scaling the workload to match demand.

Horizontal scaling means that the response to increased load is to deploy more Pods.
This is different from vertical scaling, which for Kubernetes would mean assigning more resources 
(for example: memory or CPU) to 
the Pods that are already running for the workload.

If the load decreases, and the number of Pods is above the configured minimum, the HorizontalPodAutoscaler instructs the workload resource (the Deployment, 
StatefulSet, or other similar resource) to scale back down.

## install metric api service for HPA

**check install metric server**
```
kubectl top pod
error: Metrics API not available
```

**install metric system**
```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
-----
check pod
kubectl get pod  -n kube-system  metrics-server-8ff8f88c6-pdwkm
NAME                             READY   STATUS              RESTARTS   AGE
metrics-server-8ff8f88c6-pdwkm   0/1     ContainerCreating   0          36s
```

**check metric server**
```
kubectl get pod  -n kube-system  metrics-server-8ff8f88c6-pdwkm
NAME                             READY   STATUS    RESTARTS   AGE
metrics-server-8ff8f88c6-pdwkm   0/1     Running   0          29m
Not Ready !!
```
**check pod logs**
```
kubectl logs   -n kube-system  metrics-server-8ff8f88c6-pdwkm
cannot validate certificate for 172.18.0.2 because it doesn't contain any IP SANs" node="milad-control-plane"
I1130 09:50:49.640532       1 server.go:187] "Failed probe" probe="metric-storage-ready" err="no metrics to serve"
```
**change mettric configuration**
```
kubectl edit deployments.apps -n kube-system  metrics-server
       
        - --cert-dir=/tmp
        - --secure-port=4443
        - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
        - --kubelet-use-node-status-port
        - --kubelet-insecure-tls
        - --metric-resolution=15s

 ```

**after installation**
```
kubectl top pod
NAME                CPU(cores)   MEMORY(bytes)
hello-world-0       0m           1Mi
hello-world-1       1m           1Mi
hello-world-dhnmj   1m           1Mi
```

**deploy php pod**
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: php-apache
spec:
  selector:
    matchLabels:
      run: php-apache
  replicas: 1
  template:
    metadata:
      labels:
        run: php-apache
    spec:
      containers:
      - name: php-apache
        image: k8s.gcr.io/hpa-example
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 250m
            memory: 30Mi
          requests:
            cpu: 100m
            memory: 10Mi
```

**deploy HPA**
```
apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
  name: php-apache
spec:
  minReplicas: 3
  maxReplicas: 10 # 10000
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: php-apache

  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 10
```

**apply yaml**
```
kubectl apply -f 02-HPA.yaml -n kube-system

kubectl get horizontalpodautoscalers.autoscaling
NAME         REFERENCE               TARGETS           MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   88%/10%, 1%/50%   3         10        10         61m     -> up 10 replicas
```

**increace POD By HPA**
```
kubectl get pod -A
kube-system   php-apache-69c9496c4f-9v7lg                   1/1     Running   0             62m
kube-system   php-apache-69c9496c4f-9xqsd                   1/1     Running   0             62m
kube-system   php-apache-69c9496c4f-dt82k                   1/1     Running   0             62m
kube-system   php-apache-69c9496c4f-dvf89                   1/1     Running   0             62m
kube-system   php-apache-69c9496c4f-f7c8d                   1/1     Running   0             62m
kube-system   php-apache-69c9496c4f-htvkg                   1/1     Running   0             62m
kube-system   php-apache-69c9496c4f-mcwg6                   1/1     Running   0             62m
kube-system   php-apache-69c9496c4f-ns49g                   1/1     Running   0             62m
kube-system   php-apache-69c9496c4f-tchqq                   1/1     Running   0             62m
kube-system   php-apache-69c9496c4f-xwcxd                   1/1     Running   0             147m
```
