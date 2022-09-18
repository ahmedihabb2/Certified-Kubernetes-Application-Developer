**SecurityContext** it is part of the pod and container spec. It allows you to control advanced security-related settings for containers

## What you can do with SecurityContext?

- You can customize the user ID (UID) and/or group ID (GID) that a container runs as, and whether it has root privileges.

- Allow Privilege Escalation: If set to true, this allows privilege escalation for the container that process it is running as. This means that processes in the container can gain more privileges than their parent process.

- Read-Only Root Filesystem: If set to true, the container is run with a read-only root filesystem. This means that the container cannot write to the root filesystem.

- And more things you can see in doc


### You can specify it by container level or pod level, if you used pod level it will be applied to all containers in the pod


### Example 

```yaml
apiVersion: v1
kind: Pod
metadata: 
  name: securitycontext-pod
spec: 
  containers: 
  - name: busybox 
    image: busybox:stable 
    command: ['sh', '-c', 'while true; do echo Running...; sleep 5; done'] 
    securityContext: 
      runAsUser: 3000 
      runAsGroup: 4000 
      allowPrivilegeEscalation: false 
      readOnlyRootFilesystem: tru
```