## jobs
A  Job creates one or more Pods and will continue to retry execution of the Pods until a specified number of them successfully terminate. As pods successfully complete, the Job tracks the successful completions. When a specified number of successful completions is reached, the task (ie, Job) is complete. Deleting a Job will clean up the Pods it created. Suspending a Job will delete its active Pods until the Job is resumed again.

A simple case is to create one Job object in order to reliably run one Pod to completion. The Job object will start a new Pod if the first Pod fails or is deleted (for example due to a node hardware failure or a node reboot).

You can also use a Job to run multiple Pods in parallel.

If you want to run a Job (either a single task, or several in parallel) on a schedule, see CronJob



## Cronjob
Cronjob Create job on a repeating schdule.
Syntax similar cron on Linux server.


### Practice
Create a new JOB


**job.yaml**
```
apiVersion: batch/v1
kind: Job
metadata:
  name: ping
spec:
  activeDeadlineSeconds: 600
  template:
    metadata:
      name: ping
    spec:
      containers:
        - name: ping
          image: perl:slim
          command: ["perl"]
          args: ["-Mbignum=bpi", "-wle", "print bpi(1000)"]
      restartPolicy: Never
```

```
kubectl get pod -A
default       ping-ckr22                                    0/1     ContainerCreating   0            12s
```
```
kubectl get job
ping   1/1           62s        66s
```

**delete job**
```
 kubectl delete job ping
job.batch "ping" deleted
```

**Cron job Example***
```
apiVersion: batch/v1
kind: CronJob
metadata:
  name: cronjob
spec:
  # https://crontab.guru/
  schedule: "*/1 * * * *"
  concurrencyPolicy: Allow
  failedJobsHistoryLimit: 10
  successfulJobsHistoryLimit: 10
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: cronjob
              image: busybox
              args:
                - /bin/sh
                - -c
                - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure
```
**view logs**
```
kubectl logs jobs/pi
```

### A Kubernetes CronJob to Back Up the etcd Data
```
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: backup
  namespace: kube-system
spec:
  # Run every six hours.
  schedule: "0 */6 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: backup
            # Same image as in /etc/kubernetes/manifests/etcd.yaml
            image: k8s.gcr.io/etcd-amd64:3.1.12
            env:
            - name: ETCDCTL_API
              value: "3"
            command: ["/bin/sh"]
            args: ["-c", "etcdctl --endpoints=https://127.0.0.1:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/healthcheck-client.crt --key=/etc/kubernetes/pki/etcd/healthcheck-client.key snapshot save /backup/etcd-snapshot-$(date +%Y-%m-%d_%H:%M:%S_%Z).db"]
            volumeMounts:
            - mountPath: /etc/kubernetes/pki/etcd
              name: etcd-certs
              readOnly: true
            - mountPath: /backup
              name: etcd-backup-dir
          restartPolicy: OnFailure
          nodeSelector:
            node-role.kubernetes.io/master: ""
          tolerations:
          - effect: NoSchedule
            operator: Exists
          hostNetwork: true
          volumes:
          - name: etcd-certs
            hostPath:
              path: /etc/kubernetes/pki/etcd
              type: DirectoryOrCreate
          - name: etcd-backup-dir
            hostPath:
              path: /opt/etcd-backup
              type: DirectoryOrCreate
```
```
kubectl get cronjob -n kube-system backup
NAME     SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
backup   */1 * * * *   False     0        <none>          3h12m
```
