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
