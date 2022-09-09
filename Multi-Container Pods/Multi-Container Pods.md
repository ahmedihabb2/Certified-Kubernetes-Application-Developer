## When to take this approach?

- When the containers need to be tighly coupled, sharing resources such as network and storage volumes.

### Multi-Container Design Patterns

- **Sidecar :** A sidecar container performs a tasks to assist the main container.
    -  For example, a sidecar container can be used to:
        - Perform logging and metrics collection.
        - Perform health checks.
        - Perform configuration management.
        - Perform other tasks.

- **Ambassador :** An ambassador  proxies network traffic to and/or from the main container.
    - For example, if the service port is changed the ambassador can proxy the traffic to the new service port.

- **Adapter :** An adapter container transforms the main container’s output.
    - For example, is the main container logs missing timestamp, the adapter can add the timestamp.


## Examples

- Create a Pod with a sidecar container that interacts with the main container using a shared volume.
<details>
<summary style="font-size:14px">emptyDir Volume</summary>
Note: emptyDir volume is is first created when a Pod is assigned to a node, and exists as long as that Pod is running on that node. As the name says, the emptyDir volume is initially empty. All containers in the Pod can read and write the same files in the emptyDir volume, though that volume can be mounted at the same or different paths in each container. When a Pod is removed from a node for any reason, the data in the emptyDir is deleted permanently.
</details>

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: sidecar-test
spec:
    containers:
    - name: writer
      image: busybox:stable
      command: ['sh', '-c', 'echo "The writer wrote this!" > output/data.txt; while true; do sleep 5;done']
      volumeMounts:
        - name: shared
          mountPath: /output
    - name: sidecar
      image: busybox:stable
      command: ['sh', '-c', 'while true; do cat /input/data.txt; sleep 5; done']
      volumeMounts:
      - name: shared
        mountPath: /input
    volumes:
    - name: shared
    emptyDir: {}

```
<details>
<summary style="font-size:14px">Notes</summary>
- They can interact with eachother as they both points to the same volume
- The main container writes to the volume and the sidecar reads from it
</details>


## Ambassador example

- First create a pod and service for testing 

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ambassador-test-webserver
  labels:
    app: ambassador-test
spec:
  containers:
  - name: nginx
    image: nginx:stable
    ports:
    - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: ambassador-test-svc
spec:
  selector:
    app: ambassador-test
  ports:
  - protocol: TCP
    port: 8081
    targetPort: 80
```

- Second create the pod with two containers the main and the ambassador, the main tries to connect to the test pod with wrong port number , but the ambassador takes the request and forward it to the service with right port (Acts as a proxy)



```yaml

apiVersion: v1
kind: ConfigMap
metadata:
  name: haproxy-config
data:
  haproxy.cfg: |
    frontend ambassador
      bind *:8080
      default_backend ambassador_test_svc
    backend ambassador_test_svc
      server svc ambassador-test-svc:8081
---
apiVersion: v1
kind: Pod
metadata:
  name: ambassador-test
spec:
  containers:
  - name: main
    image: radial/busyboxplus:curl
    command: ['sh', '-c', 'while true; do curl localhost:8080; sleep 5; done']
  - name: ambassador
    image: haproxy:2.4
    volumeMounts:
    - name: config
      mountPath: /usr/local/etc/haproxy/
  volumes:
  - name: config
    configMap:
      name: haproxy-config

```