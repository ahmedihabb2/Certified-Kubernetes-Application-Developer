**InitContainer:** Is a container that is run before the main container to complete a task, only when the init container has completed the main container starts up

- It can be used to delay the startup of the main container until some services are availabe

- Also used in security to consume secrets in isolation from main container.


## Example

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: init-test
spec:
  containers:
  - name: nginx
    image: nginx:stable
  initContainers:
  - name: busybox
    image: busybox:stable
    command: ['sh', '-c', 'sleep 60']
```