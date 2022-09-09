**Volume:** Provides the container with external storage outside the container file system.

- On-disk files in a container are ephemeral, which presents some problems for non-trivial applications when running in containers. One problem is the loss of files when a container crashes. The kubelet restarts the container but with a clean state. A second problem occurs when sharing files between containers running together in a Pod. The Kubernetes volume abstraction solves both of these problems

- Ephemeral volume types have a lifetime of a pod, but persistent volumes exist beyond the lifetime of a pod

- To use a volume, specify the volumes to provide for the Pod in .spec.volumes and declare where to mount those volumes into containers in .spec.containers[*].volumeMounts

### Volume:

- Is defined in a pod spec
- Also it defines the details of where and how the data is stored

### volumeMount:

- Is defined in a container spec
- Is used to mount a volume into a container
- It defined the path where the volume data will apear at run time

## Some types of volumes

### hostPath

- Data is stored in a specific location directly on the host file system, on the Kubernetes node where the Pod is running

Example:
    
```yaml 
apiVersion: v1 
kind: Pod 
metadata: 
  name: hostpath-volume-test 
spec: 
  restartPolicy: OnFailure 
  containers: 
  - name: busybox 
    image: busybox:stable 
    command: ['sh', '-c', 'cat /data/data.txt'] 
    volumeMounts: 
    - name: host-data 
      mountPath: /data 
  volumes: 
  - name: host-data 
    hostPath: 
      path: /etc/hostPath 
      type: Directory
```

### But what is the type is hostPath?

- The type is what you are pointing to. For example if you are pointing to a directory, then the type is Directory. If you are pointing to a file, then the type is File.

- But if the directoy does not exist the pod will fail to start.

- So you can use DirectoryOrCreate to create the directory if it does not exist, same thing for files

### emptyDir

- Data is stored in a temporary directory on the host file system, the data is stored in automatically managed location, data is deleted when the pod is deleted

Example 

```yaml
apiVersion: v1 
kind: Pod 
metadata: 
  name: emptydir-volume-test 
spec: 
  restartPolicy: OnFailure 
  containers: 
  - name: busybox 
    image: busybox:stable 
    command: ['sh', '-c', 'echo "Writing to the empty dir..." > /data/data.txt; cat /data/data.txt'] 
    volumeMounts: 
    - name: emptydir-vol 
      mountPath: /data 
  volumes: 
  - name: emptydir-vol 
    emptyDir: {}  # takes empty map

```