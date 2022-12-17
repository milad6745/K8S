## autoscaler project.md
 Recomend to used  esourse
**Download package from git**

```
cd /tm
git clone https://github.com/kubernetes/autoscaler.git
#then
bash /hack/vpa-up.sh
```

**check vpa is start running**
```
kubectl get pod -n kube-system
NAME                                          READY   STATUS              RESTARTS      AGE
vpa-admission-controller-64cb9cfb4b-psrxc     0/1     ContainerCreating   0             12s
vpa-recommender-6fdd69df6c-fk8hp              0/1     ContainerCreating   0             13s
vpa-updater-8567bff646-xkjcg                  0/1     ContainerCreating   0             15s

#then use vpa
```

**use vpa pod**
```
apiVersion: "autoscaling.k8s.io/v1"
kind: VerticalPodAutoscaler
metadata:
  name: php-vpa
spec:
  targetRef:
    apiVersion: "apps/v1"
    kind: Deployment
    name: php-apache
  resourcePolicy:
    containerPolicies:
      - containerName: '*'
        minAllowed:
          cpu: 100m
          memory: 50Mi
        maxAllowed:
          cpu: 1
          memory: 500Mi
        controlledResources: ["cpu", "memory"]
```

**check vpa***
```
kubectl apply -f  01-vpa.yaml
verticalpodautoscaler.autoscaling.k8s.io/php-vpa created
┌──(root💀kali)-[/home/kuber]
└─# kubectl get vpa
NAME      MODE   CPU   MEM   PROVIDED   AGE
php-vpa   Auto                          5s
```
**ADD pod to vpa**
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
**check pod connect to vpa**
```
kubectl apply -f  01-php-pod.yaml
deployment.apps/php-apache configured
┌──(root💀kali)-[/home/kuber]
└─# kubectl get vpa
NAME      MODE   CPU    MEM       PROVIDED   AGE
php-vpa   Auto   100m   262144k   True       2m12s --> Recomend esourse
```
