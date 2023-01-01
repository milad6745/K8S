## The NetworkPolicy resource

See the NetworkPolicy reference for a full definition of the resource.
An example NetworkPolicy might look like this:

service/networking/networkpolicy.yaml Copy service/networking/networkpolicy.yaml to clipboard
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: test-network-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
    - Ingress
    - Egress
  ingress:
    - from:
        - ipBlock:
            cidr: 172.17.0.0/16
            except:
              - 172.17.1.0/24
        - namespaceSelector:
            matchLabels:
              project: myproject
        - podSelector:
            matchLabels:
              role: frontend
      ports:
        - protocol: TCP
          port: 6379
  egress:
    - to:
        - ipBlock:
            cidr: 10.0.0.0/24
      ports:
        - protocol: TCP
          port: 5978
```
Mandatory Fields: As with all other Kubernetes config, a NetworkPolicy needs apiVersion, kind, and metadata fields
spec: NetworkPolicy spec has all the information needed to define
podSelector: Each NetworkPolicy includes a podSelector which selects the grouping of pods to which the policy applies
policyTypes: Each NetworkPolicy includes a policyTypes list which may include either Ingress, Egress, or both
ingress: Each NetworkPolicy may include a list of allowed ingress rules
egress: Each NetworkPolicy may include a list of allowed egress rules

**So, the example NetworkPolicy:**

1-isolates role=db pods in the default namespace for both ingress and egress traffic (if they weren't already isolated)

2- (Ingress rules) allows connections to all pods in the default namespace with the label role=db on TCP port 6379 from:

- any pod in the default namespace with the label role=frontend
- any pod in a namespace with the label project=myproject
- IP addresses in the ranges 172.17.0.0–172.17.0.255 and 172.17.2.0–172.17.255.255 (ie, all of 172.17.0.0/16 except 172.17.1.0/24)

3- (Egress rules) allows connections from any pod in the default namespace with the label role=db to CIDR 10.0.0.0/24 on TCP port 5978


**network policy Example**
we have 3 pod on my cluster for this reason

![Capture](https://user-images.githubusercontent.com/113288076/210169200-f2a1a729-115f-4f89-829d-4b74e2c0cdc1.PNG)

we want to filter access pod c to pod b with this yaml file
![Ca1pture](https://user-images.githubusercontent.com/113288076/210169233-d392cdea-5fcb-4a4c-809e-1d6239555d5c.PNG)

and then we must install calico

```
kubectl apply -f https://raw.githubusercontent.com/aws/amazon-vpc-cni-k8s/release-1.6/config/v1.6/calico.yaml
```

**other network policy sample**
![image](https://user-images.githubusercontent.com/113288076/210169395-cd4e46ee-28bc-468b-a2b7-a19cad847c21.png)


**other sample for ingeres and egress**
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: test-network-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
    - Ingress
    - Egress
  ingress:
    - from:
        - ipBlock:
            cidr: 172.17.0.0/16
            except:
              - 172.17.1.0/24
        - namespaceSelector:
            matchLabels:
              project: myproject
        - podSelector:
            matchLabels:
              role: frontend
      ports:
        - protocol: TCP
          port: 6379
  egress:
    - to:
        - ipBlock:
            cidr: 10.0.0.0/24
      ports:
        - protocol: TCP
          port: 5978
```

