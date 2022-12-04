## Kubernetes Vertical Pod Autoscaler - vpa .md

How to use VPA
Here is a sample Kubernetes Deployment that uses VPA for resource recommendations.

First, create the Deployment resource using the following YAML manifest shown below. Note that there are no CPU or memory requests. The pods in the Deployment belong to the VerticalPodAutoscaler (shown in the next paragraph) as they are designated with the kind, Deployment and name, nginx-deployment.

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.8
        ports:
        - containerPort: 80
```
Then, create the VPA resources using the following manifest:
```
apiVersion: autoscaling.k8s.io/v1beta1
kind: VerticalPodAutoscaler
metadata:
  name: nginx-deployment-vpa
spec:
  targetRef:
    apiVersion: "apps/v1"
    kind:       Deployment
    name:       nginx-deployment
  updatePolicy:
    updateMode: "Off"
```

in vpa 3 deployment running
