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
