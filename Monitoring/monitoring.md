**Monitoring :** Is the process of gathering data (metrics) about the performance of your contanierized application and then visualizing it in a dashboard.

### Steps to access data with the metrics API:

- Install Metrics Server: This is a component that gathers the metric data so that we can acess it. Without Metrics Server, the Metrics API will not provide any data.

- Access metric data: the kubectl top command can be used to view metric data once it is gathered by the Metrics Server.


### Example 

- Install  metrics-server . This command uses a yaml file with a small tweak to make it easier to get          metrics-server running in our environment.

```bash
kubectl apply -f https://raw.githubusercontent.com/ACloudGuru-Resources/content-cka-
resources/master/metrics-server-components.yaml
```
- Then apply this yaml file, it creates a pod that consumes specific resources sent to it via curl command

```yaml
apiVersion: v1 
kind: Pod 
metadata: 
  name: resource-consumer-pod 
spec: 
  containers: 
  - name: resource-consumer 
    image: gcr.io/kubernetes-e2e-test-images/resource-consumer:1.5 
  - name: busybox-sidecar 
    image: radial/busyboxplus:curl 
    command: ['sh', '-c', 'until curl localhost:8080/ConsumeCPU -d "millicores=100&durationSec=3600"; do 
sleep 5; done && while true; do sleep 10; done']
```

- After waiting a while you can run this command to see the consumed resources 

```bash
kubectl top pod 
```

## Useful resources

https://kubernetes.io/docs/tasks/debug/debug-cluster/resource-metrics-pipeline/
https://kubernetes.io/docs/tasks/debug/debug-cluster/resource-usage-monitoring/
https://github.com/kubernetes-sigs/metrics-server