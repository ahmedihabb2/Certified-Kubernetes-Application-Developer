**Ingress:** Is a Kubernetes Object that manages access to Service from outside the cluster.

- We have talked that you can expose your services by using NodePort but that is not a good way to expose your services to the outside world(Security issues). Ingress provide this functionality with more powerful features.

- Usually Ingress is used with cloud providers like AWS, GCP, Azure, etc as they provide components that manages external access (LoadBalancer). But you can also use it with on-premises clusters.


- An ingress routes traffic to a service, which then routes the traffic to a pod (works alongside with services).

### Note that you need an Ingress Controller to implement the ingress functionality by default there is no ingress controller in kubernetes.


### Example 

- First create a simple pod that listens on port 80 and returns a simple html page.

```yaml
apiVersion: v1 
kind: Pod 
metadata: 
  name: ingress-test-pod 
  labels: 
    app: ingress-test 
spec: 
  containers: 
  - name: nginx 
    image: nginx:stable 
    ports: 
    - containerPort: 80
```

- Create a service for the pod as we mentioned that ingress works with service.

```yaml
apiVersion: v1
kind: Service
metadata: 
  name: ingress-test-service
spec: 
  type: ClusterIP 
  selector: 
    app: ingress-test 
  ports: 
    - protocol: TCP 
      port: 80 
      targetPort: 80
```

- Now create an ingress object that routes the traffic to the service.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata: 
  name: ingress-test-ingress
spec: 
  ingressClassName: nginx # which ingress controller to use
  rules: 
  - host: ingresstest.example.com
    http: 
      paths: 
      - path: / 
        pathType: Prefix 
        backend: 
          service: 
            name: ingress-test-service  # name of the service
            port: 
              number: 80  # port of the service
```

- It will not work because we don't have an ingress controller. So we need to install an ingress controller. In this example we will use nginx ingress controller.

```bash
helm repo add nginx-stable https://helm.nginx.com/stable 
 
helm repo update 
 
kubectl create namespace nginx-ingress 
 
helm install nginx-ingress nginx-stable/nginx-ingress -n nginx-ingress
```

- Get the cluster IP of the Ingress controller's Service.
    
 ```bash
kubectl get svc nginx-ingress-nginx-ingress -n nginx-ingress
```

- Edit your  hosts  file.
    
```bash
sudo vi /etc/host
```

- Use the cluster IP to add an entry to the hosts file.

```
<Nginx ingress controller Service cluster IP> ingresstest.example.com
```

- Now, test your setup.

```bash
curl ingresstest.example.com
```

- We are Done :)