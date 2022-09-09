**Jobs:** Are responsible for running containerized tasks successfully to completion.

**CroneJobs:** Are responsible for running containerized tasks periodically as scheduled.

### Jobs example

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: ahmed-job
spec:
  template:
    spec:
      containers:
      - name: print
        image: busybox:stable
        command: ["echo" , "This is a Manga"]
      restartPolicy: Never
  backoffLimit: 4   # Number of times to retry the job after it fails.
  activeDeadlineSeconds: 10 # Time in seconds to wait for the job to complete after that the job will be terminated.

```
### New things to learn 

- backoffLimit: 4   # Number of times to retry the job after it fails.

- activeDeadlineSeconds: 10 # Time in seconds to wait for the job to complete after that the job will be terminated.

- The restartPolicy for jobs should be never or onFailure, because jobs are designed to run once until completion.


## CronJobs

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: ahmed-crone
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: print
            image: busybox:stable
            command: ["echo" , "This is a Manga"]
          restartPolicy: Never
      backoffLimit: 4
      activeDeadlineSeconds: 10
```
