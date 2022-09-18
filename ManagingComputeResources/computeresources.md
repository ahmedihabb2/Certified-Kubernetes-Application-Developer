## Requests VS Limits

- **Requests:** Provides Kubernetes with an idea of how many resources a container is expected to use. The cluster uses this information to select a node that has enough resources availabe

- **Limits:** Provides an upper limit on how many resources a container is allowed to use. If a container tries to use more resources than the limit, the container is terminated.

## ResourceQuota

- Is a Kubernetes object that sets limits on the resources used within a Namespace if creating or modifying resource would go beyond this limit, the request will be denied.

## Example

```yaml
apiVersion: v1 
kind: Pod 
metadata: 
  name: resources-pod 
  namespace: resources-test 
spec: 
  containers: 
  - name: busybox 
    image: busybox:stable 
    command: ['sh', '-c', 'while true; do echo Running...; sleep 5; done'] 
    resources: 
      requests: 
        memory: 64Mi 
        cpu: 250m 
      limits: 
        memory: 128Mi 
        cpu: 500m
```

- A milli-CPU is 1/1000 of a CPU core
- Mi is same as MB
- the container can use more or less than requests, it is just an approximation for kube to schedule the pod on a node that has enough resources available


- To use ResourceQuota, you need to enable its admission controller in the API server. This is done by passing the following flag to the API server:

```bash
- --enable-admission-plugins=NodeRestriction,ResourceQuota
```

- Apply RQ yaml file, you should apply it to the namespace you want to limit

```yaml
apiVersion: v1 
kind: ResourceQuota 
metadata: 
  name: resources-test-quota 
  namespace: resources-test 
spec: 
  hard: 
    requests.memory: 128Mi 
    requests.cpu: 500m 
    limits.memory: 256Mi 
    limits.cpu: "1"
```
