## autoscaler project.md

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
