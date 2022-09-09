**Deployment :** defines a desired state for a set of replica pods. Kubernetes works to maintain that desired state by creating , deleting and replicating those pods

### Example

Some Notes:

- matchLabels is a map of key/value pairs that must match labels in the pod template ( it actually tells the deployment which pods to manage).

- template defines the pod specifications every pod will be created from that template.

- replicas is the number of replicas of the pods that will be created.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: nginx-deployment
spec: 
  replicas: 2 
  selector: 
    matchLabels:   
      app: nginx 
  template: 
    metadata: 
      labels: 
        app: nginx 
    spec: 
      containers: 
      - name: nginx 
        image: nginx:1.14.2 
        ports: 
        - containerPort: 80
```

### How to scale deployment

```bash
kubectl scale deployment nginx-deployment --replicas=3
or
kubectl edit deployment nginx-deployment
```

## Rolling update

- Kubernetes will gradually update the pods to the new version, it will not replace the pods immediately because this will cause a down time for a period and the new update may fail so the system will be down, but it creates a new pod and when it is running it will replace the old pod.

### Example 

- In the previos example we have created a deployment with nginx image verion 1.14.2 , we will update this verion and see how kubernetes will perform the update

- You can change the image of the pod template using
```bash
kubectl edit deployment nginx-deployment
```
- Or you can use this command , nginx here is he name of the container
```bash
kubectl set image deployment.v1.apps/nginx-deployment nginx=nginx:1.16.1 
```

- After that you can view the states of the update by using this command
```bash
kubectl rollout status deployment nginx-deployment
```
But what if the update fails?

- You can rollback the update by using this command
```bash
kubectl rollout undo deployment nginx-deployment
```

## Deployment strategy

### Blue/green deployment

- A blue/green deployment strategy involves using two identical production environments, new code is rolled out to the new environment and then the old environment is kept up and running , until the new code is confirmed to be working. We route the users traffic to it

#### Example

- Fisrt we will create a deployment called blue deployment and we will use a custom image that respondes with Blue or Green depending on which tag we will use 

```yaml
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: blue-deployment
spec: 
  replicas: 1 
  selector: 
    matchLabels: 
      app: bluegreen-test 
      color: blue 
  template: 
    metadata: 
      labels: 
        app: bluegreen-test 
        color: blue 
    spec: 
      containers: 
      - name: nginx 
        image: linuxacademycontent/ckad-nginx:blue 
        ports: 
        - containerPort: 80
```

- Second we will create a service that redirects the traffic to the blue deployment

```yaml
apiVersion: v1
kind: Service
metadata: 
  name: bluegreen-test-svc
spec: 
  selector: 
    app: bluegreen-test    #Notice here we added the labels of the blue deployment
    color: blue 
  ports: 
  - protocol: TCP 
    port: 80 
    targetPort: 80
```

- lets ensure that the service is running

```bash
kubectl get services
# Then curl the serive IP You should see I'm Blue !
```

- After that let's create a deployment called green deployment, we will use the same yaml file used for blue one but with different label and image tag

```yaml
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: green-deployment
spec: 
  replicas: 1 
  selector: 
    matchLabels: 
      app: bluegreen-test 
      color: green 
  template: 
    metadata: 
      labels: 
        app: bluegreen-test 
        color: green 
    spec: 
      containers: 
      - name: nginx 
        image: linuxacademycontent/ckad-nginx:green 
        ports: 
        - containerPort: 80
```

- Let's ensure that the new deployment are working well

```bash
kubectl get pods -o wide
# Then curl the pod IP You should see I'm Green !

```
- Hmmm..., now the green one works well let's redirect users traffic to it by editing the service deployment label selector

```yaml
spec:
  selector:
    app: bluegreen-test 
    color: green
```

- We are done ^_^

### Canary deployment

- A portion of the user base is used to test the new code, and the rest of the user base is used to serve the old code.

#### Example

- The simplest way to make a canary deployment is to create two deployment (main and canary) and then create a service that redirects the traffic to both deployments by using the same label for the two deployments as following

```yaml 
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: main-deployment
spec: 
  replicas: 3 
  selector: 
    matchLabels: 
      app: canary-test  # Notice this label here :D
      environment: main 
  template: 
    metadata: 
      labels: 
        app: canary-test 
        environment: main 
    spec: 
      containers: 
      - name: nginx 
        image: linuxacademycontent/ckad-nginx:1.0.0 
        ports: 
        - containerPort: 80
```

```yaml
apiVersion: v1
kind: Service
metadata: 
  name: canary-test-svc
spec: 
  selector: 
    app: canary-test  # Notice this label here :D
  ports: 
  - protocol: TCP 
    port: 80 
    targetPort: 80
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: canary-deployment
spec: 
  replicas: 1 
  selector: 
    matchLabels: 
      app: canary-test # Same label as the main deployment
      environment: canary 
  template: 
    metadata: 
      labels: 
        app: canary-test 
        environment: canary 
    spec: 
      containers: 
      - name: nginx 
        image: linuxacademycontent/ckad-nginx:canary 
        ports: 
        - containerPort: 80
```

- Now if you do a curl on deployment you will get responses from the main deployment and the canary deployment

- **But how to make a little portion of users use the canary one?** , simply but increasing the number of replicas of the main deployment , if main has 4 replicas and canary has 1 replica then the canary deployment will be used by 25% of the users 