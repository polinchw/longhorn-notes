# Rancher Longhorn Notes


## Outline

+ Overview
+ Install Longhorn in Rancher
+ Using Minio for Backups
+ Setting up Longhorn to use Minio for Backups
+ Taking a Backup of a PVC
+ Restoring a PVC

### Overview

I started using Longhorn as a Persistent Storage Class in Kubernetes.
There were a few things I really liked about it:

+ Easy to set up
+ Supports backups and recovery of a Kubernetes PVC

### Install Longhorn in Rancher

Installing Longhorn in a Rancher Kubernetes cluster is easy.  You can
install it from Rancher's App Catalog.  Once Longhorn is installed you 
can used it as a Storage Class for your PVCs.


![Diagram](screenshots/setup-longhorn.png)

### Using Minio for Backups

I set up Minio as for storing PVC backups `offsite`.  Minio uses the 
Amazon S3 protocol.  I created a bucket in Minio called `backup`.  Longhorn
will be set up to send backups to 

![Diagram](screenshots/minio.png)

### Setting up Longhorn to use Minio for Backups

#### Secret

Longhorn will need a Kubernetes Secret in the longhorn namespace to 
authenticate with Minio. This was tricky to set up because the values
needed to work we're so intuative.

```
apiVersion: v1
data:
  AWS_ACCESS_KEY_ID: QUtJQUlPU0ZPRE5ON0VYQU1QTEU= #this is base64 coded
  AWS_ENDPOINTS: aHR0cDovLzQwLjEzOC4zNS41Mg== #this is a base64 encoded URL http://40.138.35.52
  AWS_SECRET_ACCESS_KEY: d0phbHJYVXRuRkVNSS9LN01ERU5HL2JQeFJmaUNZRVhBTVBMRUtFWQ== #this is base64 coded
kind: Secret
metadata:
  name: minio
  namespace: longhorn-system
type: Opaque


```

#### Longhorn Config

![Diagram](screenshots/longhorn-backup-setting.png)


### Taking a Backup of a PVC

After Longhorn is configured to point to Minio.  You can take a backup.  From this screen.

![Diagram](screenshots/volume-backup.png)