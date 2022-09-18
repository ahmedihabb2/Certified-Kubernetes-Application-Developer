**ServiceAccounts:** allows processes within container to authenticate with the Kubernetes API server. They can be assigned permissions via role-based access control, just like regular user accounts.

- ServiceAccounts uses ServiceAccount Tokens. A token is the credential used to access the API using the ServiceAccount. The token for Pod's ServiceAccount is automatically mount to each container.

### Example

- First we will create a service account 

```yaml
apiVersion: v1
kind: ServiceAccount
metadata: 
  name: my-sa
automountServiceAccountToken: true # Automatic attach the token to the pod as a voulmeMount
```

- Secondly we will create a pod that uses the service account

```yaml
apiVersion: v1 
kind: Pod 
metadata: 
  name: sa-pod 
spec: 
  serviceAccountName: my-sa 
  containers: 
  - name: busybox 
    image: radial/busyboxplus:curl 
    command: ['sh', '-c', 'while true; do curl -s --header "Authorization: Bearer $(cat 
/var/run/secrets/kubernetes.io/serviceaccount/token)" --cacert 
/var/run/secrets/kubernetes.io/serviceaccount/ca.crt https://kubernetes/api/v1/namespaces/default/pods; 
sleep 5; done']
```
What this command do is :
   
    - It fisrt authenticate with the API server using the token that is mounted to the pod into the path /var/run/secrets/kubernetes.io/serviceaccount/token

    - Then it uses the CA certificate that is mounted to the pod into the path /var/run/secrets/kubernetes.io/serviceaccount/ca.crt to tust the API

    - Then it uses the API to get the list of pods in the default namespace

**At this point, the Pod log should display an error message indicating that the ServiceAccount does not have appropriate
permissions to perform the requested API call** To solve this see Kubernetes Auth section

But we successfully authenticated the Pod with the API server using the ServiceAccount token.