**Probe :** Probes are part of container spec in Kubernetes. They allow you to customize how Kubernetes detects the state of a container. Essentially they allow us to implement different types of health checks for our containers. 


## Probes Types

### Liveness Probe

- Liveness probes check whether a container is healthy so that it can be restarted if it is not.

#### Example

```yaml
apiVersion: v1 
kind: Pod 
metadata: 
  name: liveness-pod 
spec: 
  containers: 
  - name: busybox 
    image: busybox:stable 
    command: ['sh', '-c', 'while true; do sleep 10; done'] 
    livenessProbe: 
      exec: 
        command: ['echo', 'health check!']   # execute command to see how container will repond
      initialDelaySeconds: 5  # wait for 5 seconds from conatiner start up before starting the first probe
      periodSeconds: 5  # wait for 5 seconds between each probe
```

after applying this example you should see your pod is up and running and no restarts


### Readiness Probe

- Readiness probes determine when a container is fully stared up and ready to receive traffic.

#### Example

```yaml
apiVersion: v1
kind: Pod
metadata: 
  name: readiness-pod
spec: 
  containers: 
  - name: nginx 
    image: nginx:1.20.1 
    ports: 
    - containerPort: 80 
    livenessProbe: 
      httpGet: 
        path: / 
        port: 80 
      initialDelaySeconds: 3 
      periodSeconds: 3 
    readinessProbe: 
      httpGet: 
        path: / 
        port: 80 
      initialDelaySeconds: 15 
      periodSeconds: 5
``` 

After applying this example if you make kubectl get pods directly you will see the pod is running but it is node ready yet , as the readinessProbe doesn't pass yet. due to the initialDelaySeconds after 15 seconds you will see the pod is ready and you can access the nginx server.

### Startup Probe

- Startup probes are special case designed for slow starting containers that takes a long time to get up and running. It is kind of like a liveness probe but it is only checked during the container startup.

- Its purpose to prevent the container from being considered unhealthy during that extra long startup time.