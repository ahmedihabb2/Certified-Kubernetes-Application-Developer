**Kubernetes API :** is the primary interface for Kubernetes , when typing commands with kubectl then you are interacting with Kubernetes API 

**Deprecation :** is the process of providing advanced warning of changes to an API , giving consumers of that API time to update their code and/or processes.
(in other words if there are new verion of that API then the old version will be deprecated and the new version will be used instead)

### apiVersion

- The apiVersion feild on a kubernetes object indicates which version of the API it is designed to be compatible with.

### Deprecation Window

- The deprecation window is the time period in which the old version of the API is still supported after the new version is released.

- GA (General Availability) released API versions are supported for 12 months or 3 releases (whichever is longer).

Visit this url for Deprecation policy: https://kubernetes.io/docs/reference/using-api/deprecation-policy/


### Migration Guide

- The migration guide is a document that describes the changes required to migrate from one version of an API to another.

visit this url: https://kubernetes.io/docs/reference/using-api/deprecation-guide/ for more information about deprecation and migration guide