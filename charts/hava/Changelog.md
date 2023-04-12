# Change Log

## 0.8.1  ![AppVersion: v2.2.705](https://img.shields.io/static/v1?label=AppVersion&message=v2.2.705&color=success&logo=) ![Kubernetes: >=1.19.0-0 <1.25](https://img.shields.io/static/v1?label=Kubernetes&message=%3E%3D1.19.0-0&color=informational&logo=kubernetes) ![Helm: v3](https://img.shields.io/static/v1?label=Helm&message=v3&color=informational&logo=helm)
---

**Release date:** 2023-04-12

* Removed kuberentes version check from chart as it was breaking installs on EKS clusters

## 0.8.0  ![AppVersion: v2.2.705](https://img.shields.io/static/v1?label=AppVersion&message=v2.2.705&color=success&logo=) ![Kubernetes: >=1.19.0-0 <1.25](https://img.shields.io/static/v1?label=Kubernetes&message=%3E%3D1.19.0-0&color=informational&logo=kubernetes) ![Helm: v3](https://img.shields.io/static/v1?label=Helm&message=v3&color=informational&logo=helm)
---

**Relase date:** 2023-03-31

* Feature: Support Hosting in Azure
* Feature: Support Azure Storage for static data
* Feature: New auto-generated environment of all resources in source
* Feature: Security views for default VPCs are now hidden by default
* Security: Updated libraries and minor security enhancements
* Improvements: Removed unnecessary log entries
* Improvements: Updated and automated price import for self hosted
* UI Improvements: Add show/hide toggle on source list
* UI Improvements: Adjustments to UI for better user experience
* UI Improvements: Defaults changed to display all connections and names
* Performance: 5x speed increase on diagram drawing
* Bug: Fix switching views when list view is selected first
* Bug: Don't display checkbox on toggles in Safari
* Bug: Fix packet mirroring connections via tags in GCP
* Bug: Fix firewall connections for instances in GCP
* Bug: Fix retry logic for environment share
* Debug: Allow disabling of CORS to debug connectivity issues

### Default Value Changes

```diff
diff --git a/charts/hava/values.yaml b/charts/hava/values.yaml
index 3154544..863ce4f 100644
--- a/charts/hava/values.yaml
+++ b/charts/hava/values.yaml
@@ -36,7 +36,7 @@ environment:
 # This block is for setting the image used by the Hava pods. Changing if a private container repository is needed
 image:
   repository: hava/self-hosted
-  tag: 2.1.589
+  tag: 2.2.705
   pull_policy: IfNotPresent
   # Set this to use a secret to authenticate with a private container registry
   pull_secret_name:
@@ -67,6 +67,8 @@ web:
   ingress:
     # enabled default ingress, set to false to configure different ingress, e.g. gloo or nginx
     enable: true
+    # The type of ingress to use. For historic reasons, this defaults to AWS. Supported values: AWS, AZURE
+    type: AWS
     # add cert ARN here if ssl is set to true in environment
     acm_cert_arn: ""
 # This block configures the notify pods
@@ -149,6 +151,10 @@ render:
   # CPU and memory limits of the pods, increase to scale pods vertically
   cpu: 250m
   memory: 2048M
+  # where renders will be stored when completed, supported values: s3, azure-blob
+  storage_type: s3
+  # prefix added to path for files stored in storage location
+  storage_prefix: ""
   # Configuration for storage of render outputs in S3
   aws:
     # set to true to use role based access
@@ -162,6 +168,14 @@ render:
     access_key: "example"
     # aws secret key for the user with access to the S3 bucket (if using a user)
     secret_key: "example"
+  # settings used when storage_type is set to azure
+  azure:
+    # name of storage account to store renders
+    storage_account: ""
+    # name of the blob container to store renders in
+    blob_container: ""
+    # secret access key used to access storage account
+    access_key: ""
 
 # This block configures the background worker pods
 worker:
@@ -234,7 +248,7 @@ cache:
 storage:
   ## if the chart will define storage classes or not
   enable: true
-  ## type of persistent storage, supported values: AWS, GCP, Azure, local
+  ## type of persistent storage, supported values: AWS, azure-blob, local
   type: AWS
 
 elasticsearch:
@@ -247,6 +261,10 @@ elasticsearch:
   image: hava/elasticsearch
   # the tag of the image
   imageTag: 6.8-s3-backup-1.0.6
+  esConfig:
+    # sets the max size of imported documents
+    elasticsearch.yml: |
+      http.max_content_length: 500mb
   backup:
     # Change this to disable backups for the elasticsearch deployment
     enabled: true
```