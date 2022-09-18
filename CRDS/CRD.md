**Custom Resource:** A custom resource is an extension of the Kubernetes API. It  allows you to define your own custom Kubernetes objects types and interact with them, just as you would with other Kubernetes objects (like pods , deployments ,....).

And we can interact with them using kubectl like
```bash

kubectl get myresource
```
You can create **Cutstom Controllers** that act upon custome resources

### Example

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata: 
  name: beehives.acloud.guru # plural.group
spec: 
  group: acloud.guru   # The organization that owns the CRD
  names:  # names that we are going to use to refer to objects of this type of CRD , kubectl get ????
    plural: beehives 
    singular: beehive 
    kind: BeeHive   # kind to be used in yaml files to create object of this type
    shortNames: 
    - hive 
  scope: Namespaced  # Available to specific Namespace or for the whole cluster
  versions: 
  - name: v1 
    served: true 
    storage: true 
    schema:  # Data fields that are allowed in the object
      openAPIV3Schema: 
        type: object 
        properties: 
          spec: 
            type: object 
            properties: 
              supers: 
                type: integer 
              bees: 
                type: integer
```

- Then run 
```bash
kubectl apply -f beehive-crd.yaml
```

- Ensure that the CRD created by 
```bash
kubectl get crd
```

- Create a beehive object
```yaml
apiVersion: acloud.guru/v1  # the group name we have created
kind: BeeHive
metadata: 
  name: test-beehive
spec: 
  supers: 3 
  bees: 60000
```