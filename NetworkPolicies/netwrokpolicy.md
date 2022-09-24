## Kubernetes Networking

- The Kubernetes cluster uses a **virtual network** to allow pods to communicate with one another seamlessly, even if they are on different nodes.

## NetworkPolicy

- is a Kubernetes object that allows you to restrict netwrok traffic to and from Pods within the cluster network.

- You can use it to block unnecessary or unexpected network traffic and make your applications more secure.

## Non-Isolated Pods

- Any pod that is not selected by any NetworkPolicies

- Open to all icoming and outgoing network traffic (NetworkPolicies don't apply)

## Isolated Pods

- Any pod that is selected by at least one NetworkPolicy

- Only open to traffic that is explicitly allowed by the NetworkPolicy


## Example

- Let's cretae two namespaces to apply networkpolicies
```bash
kubectl create namespace test-a

kubectl create namespace test-b
```

- Attach lables to these namespaces
```bash
kubectl label namespace test-a team=ateam 
 
kubectl label namespace test-b team=bteam
```

- Let's create two pods in each namespace, one acts as server and the other one as client
```yaml
apiVersion: v1 
kind: Pod 
metadata: 
  name: server-pod 
  namespace: test-a 
  labels: 
    app: test-server 
spec: 
  containers: 
  - name: nginx 
    image: nginx:stable 
    ports: 
    - containerPort: 80
```
- The client pod will try to access the server pod using its ip  you can get the ip of the server pod using the following command
```bash
kubectl get pods -n test-a -o wide
```

```yaml
apiVersion: v1 
kind: Pod 
metadata: 
  name: client-pod 
  namespace: test-b 
  labels: 
    app: test-client 
spec: 
  containers: 
  - name: busybox 
    image: radial/busyboxplus:curl 
    command: ['sh', '-c', 'while true; do curl -m 2 192.168.140.17; sleep 5; done']
```

- Until now the client pod should access the server pod normally without any errors

- Let's start creating some restrictions!!

```yaml
apiVersion: networking.k8s.io/v1 
kind: NetworkPolicy 
metadata:
  name: test-a-default-deny-ingress 
  namespace: test-a 
spec:
  podSelector: {}   # Empty will select all pods in the namespace
  policyTypes: 
  - Ingress # Deny all ingress traffic , we can specify egress also
```

- Now the client pod can't access the server pod anymore

- Let's create a network policy to allow the client pod to access the server pod

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata: 
  name: test-client-allow 
  namespace: test-a
spec: 
  podSelector: 
    matchLabels: 
      app: test-server   # Select the server pod
  policyTypes: 
  - Ingress 
  ingress: 
  - from: 
    - namespaceSelector:  # Select the client pod namespace
        matchLabels: 
          team: bteam 
      podSelector:       # Select the client pod , it must achieve both conditions
        matchLabels: 
          app: test-client 
    ports: 
    - protocol: TCP 
      port: 80  # The port that the client pod will try to access
```

- Note that NetworkPolicies override each other, so if you have two NetworkPolicies that select the same pod, the pod will be isolated by the NetworkPolicy that was created last.

- Now the client pod can access the server pod again


- Hmmmm... Let's create an egree rule that blocks the client again form reaching the server

```yaml
apiVersion: networking.k8s.io/v1 
kind: NetworkPolicy 
metadata:
  name: test-b-default-deny-egress 
  namespace: test-b 
spec:
  podSelector: {} 
  policyTypes: 
  - Egress
```

- you can another egress rules that allow the client pod to access the server pod as we have done above for ingress

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata: 
  name: test-client-allow-egress 
  namespace: test-b
spec: 
  podSelector: 
    matchLabels: 
      app: test-client 
  policyTypes: 
  - Egress 
  egress: 
  - to: 
    - namespaceSelector:  # to any pod in this namespace
        matchLabels:  
          team: ateam 
    ports: 
    - protocol: TCP 
      port: 80
```