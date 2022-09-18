## There is 2 ways to pass ConfigMap and secre data to containers...

### Volume Mounts VS Environment Variables

- **Volume Mounts:** Data appears in the container's file system at runtime. Each top-level key in the config data becomes a file name.

- **Environment Variables:** Data appears as environment variables visible to the container at runtime. You can specify specific keys and variable names for each piece of data.


### Example

- Data in configMap can be written in key-value pairs or as a file

```yaml
apiVersion: v1
kind: ConfigMap
metadata: 
  name: my-configmap
data: 
  message: Hello, World! 
  app.cfg: |
    # A configuration file! 
    key1=value1 
    key2=value2
```

- Then create a pod that uses the configMap, we do it by two ways (volume mount and env variable)


```yaml
apiVersion: v1 
kind: Pod 
metadata: 
  name: cm-pod 
spec: 
  restartPolicy: Never 
  containers: 
  - name: busybox 
    image: busybox:stable 
    command: ['sh', '-c', 'echo $MESSAGE; cat /config/app.cfg'] 
    env: 
    - name: MESSAGE 
      valueFrom: 
        configMapKeyRef: 
          name: my-configmap 
          key: message 
    volumeMounts: 
    - name: config 
      mountPath: /config 
      readOnly: true 
  volumes: 
  - name: config 
    configMap: 
      name: my-configmap 
      items:  # if you don't specify items, all keys will be mounted as files
      - key: app.cfg 
        path: app.cfg
```

## Same thing for secrets but we must base64 encode the data first using
```bash
echo Secret Stuff! | base64 
 
echo Secret stuff in a file! | base64
```

- Create a secret

```yaml
apiVersion: v1 
kind: Secret 
metadata: 
  name: my-secret 
type: Opaque 
data: 
  sensitive.data: U2VjcmV0IFN0dWZmIQo= 
  passwords.txt: U2VjcmV0IHN0dWZmIGluIGEgZmlsZSEK
```

- Then create a pod like the one above but with secret instead of configMap


