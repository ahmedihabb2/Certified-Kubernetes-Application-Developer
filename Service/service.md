**Service:** allows you to expose an application running across multiple pods to the network

- Clients can communicate with the Service, and traffic will be automatically routed to one of the underlying Pods

## Types of Services

### ClusterIP

- A ClusterIP Serivce exposes the application within the cluster network where it can be accessed by other Pods

### NodePort

- A NodePort Service exposes the application externally by listening on a port on each node in the cluster


## Example

- Let's create a simple nginx pod

```yaml
apiVersion: v1 
kind: Pod 
metadata: 
  name: service-server-pod 
  labels: 
    app: service-server 
spec: 
  containers: 
  - name: nginx 
    image: nginx:stable 
    ports: 
    - containerPort: 80
```

- Create an ClusterIP service 

```yaml
apiVersion: v1
kind: Service
metadata: 
  name: clusterip-service
spec: 
  type: ClusterIP 
  selector: 
    app: service-server   # same as the pod label
  ports: 
    - protocol: TCP 
      port: 8080  # port to expose the service on (will be used to access the service)
      targetPort: 80 # the port the traffic will be forwarded to (container port)
```

- Now you can get the service ip using the following command

```bash
kubectl get svc
```

- Try to curl this ip from your control-plane node and it will respond