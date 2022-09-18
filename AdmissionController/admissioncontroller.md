**Admission Controller:** it intercept requests to the Kubernetes API after authentication and authorization but before any objects are persisted. They can be used to validate, deny or even modify the requests

### Usecase:

- Let's say that you are creating a pod with this manifest:
```yaml
apiVersion: v1 
kind: Pod 
metadata: 
  name: new-namespace-pod 
  namespace: new-namespace 
spec: 
  containers: 
  - name: busybox 
    image: busybox:stable 
    command: ['sh', '-c', 'while true; do echo Running...; sleep 5; done']
```

- This mainfest applying the pod in a namespace called `new-namespace` which doesn't exist, so yoy will get an error

- But what if you want that the namespace created automatically and getrid of the error?... then you should use an admission controller to do that for you

- There are lots of admission controllers, but we will use the `NamespaceAutoProvision` admission controller

- Change the API Server configuration to enable the  NamespaceAutoProvision  admission controller (in control plane node)
```bash
sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
```

- Then edit this line to be like this:
```yaml
- --enable-admission-plugins=NodeRestriction,NamespaceAutoProvision
```