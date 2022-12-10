## Endpoint Slice
Kubernetes' EndpointSlice API provides a way to track network endpoints within a Kubernetes cluster.
EndpointSlices offer a more scalable and extensible alternative to Endpoints

**Create A new Endpoint**
```
apiVersion: discovery.k8s.io/v1
kind: EndpointSlice
metadata:
  name: example-abc
  labels:
    kubernetes.io/service-name: example
addressType: IPv4
ports:
  - name: http
    protocol: TCP
    port: 80
endpoints:
  - addresses:
      - "10.1.2.3"
    conditions:
      ready: true
    hostname: pod-1
    nodeName: node-1
    zone: us-west2-a
    ```
