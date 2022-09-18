## Authentication VS Authorization

- **Authentication:** Is the user who they calim to be?
- **Authorization:** Is the user allowed to do what they are trying to do? (Do they have the right permissions?)

## Users VS ServiceAccounts

- **Users:** are the actual people who use the cluster or a tool outside the cluster , Usually authenticates using client certificates, Not represented by a Kubernetes Object.
- **ServiceAccounts:** Usually used by automated process running inside the cluster, Usually authenticates using a token, Represented by a Kubernetes Object.

### The common thing is that they both receive Authorization using RBAC (Role Based Access Control).

## What is RBAC?

- is a system for managing authorization. It allows you to define what users, or groups of users, are allowed to do in the cluster.

### Example

- First let's create a role that allows us to read pods in the default namespace.

```yaml
apiVersion: rbac.authorization.k8s.io/v1 
kind: Role 
metadata: 
  name: list-pods-role
rules: 
- apiGroups: [""]  # as we are using the core API, we leave this empty
  resources: ["pods"] # The object we are dealing with
  verbs: ["list"] # The action we are allowed to do
```

- Second, we create a role binding that binds the role to the serviceaccount that we have done before (visit ServiceAccounts section).

```yaml
apiVersion: rbac.authorization.k8s.io/v1 
kind: RoleBinding 
metadata: 
  name: list-pods-rb
subjects: 
- kind: ServiceAccount  # here you can list multiple service accounts, users
  name: my-sa 
  namespace: default 
roleRef: 
  kind: Role 
  name: list-pods-role 
  apiGroup: rbac.authorization.k8s.io
```