**persistentVolumes:** Allows you to abstract volume storage details away from pods and treat storage like a consumable resource like CPU and memory

## PV

- Defines an abstract storage resource ready to be consumable by Pods

- Also defines the details about the type of storage to be used and amount of the storage provided


## PVC

- Defines a request for storage including details on the type of storage needed

- Automatically binds to an avilable PV that meets the requirements

- Mounted in a Pod like any volume