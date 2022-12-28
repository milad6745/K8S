## multi container in a pod
multi pod use for thes reson
- backup
- syncronizing
- schduler

![mc](https://user-images.githubusercontent.com/113288076/209784731-ef90a2f6-62dc-417f-805b-6561597d126a.PNG)

## container communucate inside a pod
network name space
- containercan talk via local host and port
![C1](https://user-images.githubusercontent.com/113288076/209790361-d20ede95-6585-472c-853f-e80afcf6cf51.PNG)

```
apiVersion: v1
kind: Pod
metadata:
  name: two-containers
spec:

  restartPolicy: Never

  volumes:
  - name: shared-data
    emptyDir: {}

  containers:

  - name: nginx-container
    image: nginx
    volumeMounts:
    - name: shared-data
      mountPath: /usr/share/nginx/html

  - name: debian-container
    image: debian
    volumeMounts:
    - name: shared-data
      mountPath: /pod-data
    command: ["/bin/sh"]
    args: ["-c", "echo Hello from the debian container > /pod-data/index.html"]
```

**for check container open port**
```
 kubectl exec -it hello-world-1 -c sidecar -- /bin/bash
 ```
