# Change Log

## 1.2.0 ![AppVersion: v2.3.2686](https://img.shields.io/static/v1?label=AppVersion&message=v2.2.705&color=success&logo=) ![Kubernetes: >=1.22.0-0 <1.25.0-0](https://img.shields.io/static/v1?label=Kubernetes&message=%3E%3D1.19.0-0&color=informational&logo=kubernetes) ![Helm: v3](https://img.shields.io/static/v1?label=Helm&message=v3&color=informational&logo=helm)

* Updated chart to use forked version of ElasticSearch HELM dependency to support newer version of kubernetes

## 1.1.0 ![AppVersion: v2.3.2686](https://img.shields.io/static/v1?label=AppVersion&message=v2.2.705&color=success&logo=) ![Kubernetes: >=1.22.0-0 <1.25.0-0](https://img.shields.io/static/v1?label=Kubernetes&message=%3E%3D1.19.0-0&color=informational&logo=kubernetes) ![Helm: v3](https://img.shields.io/static/v1?label=Helm&message=v3&color=informational&logo=helm)

* Performance: Big improvement in load times on API requests
* Feature: AWS Glacier Vaults now supported
* Feature: Added connections between WAFv2 and API Gateway
* Feature: Added connections between WAFv2 and Cloudfront
* Feature: New region supported - il-central-1
* Feature: Azure US Government Cloud now supported by setting AZURE_ENVIRONMENT flag

```diff
diff --git a/charts/hava/values.yaml b/charts/hava/values.yaml
index 0ab6ca0..416e5dd 100644
--- a/charts/hava/values.yaml
+++ b/charts/hava/values.yaml
@@ -33,7 +33,7 @@ environment:
 # This block is for setting the image used by the Hava pods. Changing if a private container repository is needed
 image:
   repository: hava/self-hosted
-  tag: 2.3.880
+  tag: 2.3.2686
   pull_policy: IfNotPresent
   # Set this to use a secret to authenticate with a private container registry
   pull_secret_name:
@@ -253,4 +253,7 @@ aws:
   # Set to a comma delimited list of regions to support, i.e. us-west-1,us-west-2. If blank it will import all regions.
   regions:
   # Set to false if you don't allow govcloud data to be imported
-  govcloud_enabled: false
\ No newline at end of file
+  govcloud_enabled: false
+
+azure:
+  environment: azurecloud
\ No newline at end of file

```

## 1.0.1 ![AppVersion: v2.3.880](https://img.shields.io/static/v1?label=AppVersion&message=v2.2.705&color=success&logo=) ![Kubernetes: >=1.19.0-0 <1.25.0-0](https://img.shields.io/static/v1?label=Kubernetes&message=%3E%3D1.19.0-0&color=informational&logo=kubernetes) ![Helm: v3](https://img.shields.io/static/v1?label=Helm&message=v3&color=informational&logo=helm)

* Bug: Resolved issue with price imports not loading correctly

## 1.0.0 ![AppVersion: v2.3.877](https://img.shields.io/static/v1?label=AppVersion&message=v2.2.705&color=success&logo=) ![Kubernetes: >=1.19.0-0 <1.25.0-0](https://img.shields.io/static/v1?label=Kubernetes&message=%3E%3D1.19.0-0&color=informational&logo=kubernetes) ![Helm: v3](https://img.shields.io/static/v1?label=Helm&message=v3&color=informational&logo=helm)

Migration guide can be found in [the official documentation](https://developer.hava.io).

* Feature: Diff View
* Feature: Alerts 
* Feature: SNS display and connections
* Feature: Display connections for SQS to Lambda
* Feature: Display connections between DynamoDB and Lambda
* Feature: Added new CLI for management and troubleshooting 
* Feature: Display load balancer connections to WAFv2
* Feature: Display connect from S3 to SNS, SQS, and Lambda using notification configuration
* Feature: Display connections between API Gateway and Lambda
* Feature: Improve connection identification between route53 and CloudFront distributions
* Improvement: Updated pricing data for AWS, Azure, and GCP 
* Improvement: Simplified backend workers, now running combined workers rather than multiple types for easier management
* Improvement: Shares will now be removed when deleting an environment
* Improvement: Upgraded dependent libraries for exporting diagrams
* Improvement: API Gateway searches will now correctly return VPC Link resources
* Improvement: Improved resource identification in environment created  for default Kubernetes clusters
* Improvement: Fix email layouts
* Improvement: Improved user experience for adding new AWS sources
* Improvement: Give a useful error for the search if hit a 502
* Performance: Reviewed and added missing indexes to db
* Performance: Cache environment data between page loads
*  Security: Team member delete no longer returns details about the removed user. If the user doesn’t exist it just returns success
* Security: Added limit to characters allowed on signup form
* Bug: Fixed issue with S3 signed URL creation for export jobs
* Bug: Fixed issue with logging of AWS Route Tables that have an unsupported type
* Bug: Fixed issue with missing default value for DynamoDB key_schema as it's not always defined
* Bug: Fixed issue with startup when values are missing by setting reasonable defaults
* Bug: Fixed attributes for Regional URL Map in GCP
* Bug: Fixed account selection after a login redirect
* Bug: Fixed issues with importing CloudRun resources that don't have push_config enabled

### Breaking Changes

* CORS defaults to disabled
* DNS Changes
   * Single DNS entry required now, rather than 3 used in the past. 
   * Ingress changes may trigger new deployment of Load Balancer and DNS will need to be updated in some cases
   * default subdomain has been changed to `hava`
* Several workers removed, custom scaling config needs to be updated
   * Build
   * import
   * render
   * report
   * worker
* [AWS] New cheaper storage class based on GP2 is the default
   * Ensure you update your config with the old storage class (`hava-ssd`) to ensure a smooth migration

### Default Value Changes

```diff
diff --git a/charts/hava/values.yaml b/charts/hava/values.yaml
index 75570c7..830b2c2 100644
--- a/charts/hava/values.yaml
+++ b/charts/hava/values.yaml
@@ -4,27 +4,22 @@
 
 setup:
   license_user: hava
-  license_email: tom@hava.io
+  license_email: example@hava.io
   license: 
 
 environment:
   # dns domain the end user will connect on to access hava, e.g. hava.io 
   # this is used for cors, to it needs to match
   domain: "example.com"
-  # The subdomain that the app runs on, default is 'app' which will set up the application on e.g. app.hava.io if hava.io is set as the domain
-  web_subdomain: "app"
-  # the subdomain that the api runs on, defauilt is 'api' which will set up the api on e.g. api.hava.io if hava.io is set as the domain
-  # this can be the same as the web subdomain
-  api_subdomain: "api"
-  # the subdomain that the notification endpoint runs on, defauilt is 'notify' which will set up the notify on e.g. notify.hava.io if hava.io is set as the domain
-  websocket_subdomain: "notify"
+  # The subdomain that the app runs on, default is 'hava' which will set up the application on e.g. hava.hava.io if hava.io is set as the domain
+  web_subdomain: "hava"
   # used to enable or disable ssl for the endpoints. If set to true, ingress needs to be configured with a valid ssl certificate
   ssl: "false"
   # This is used to add additional allowed sources for CORS, needs to be fully qualified domains including protocol and port. e.g. 'https://qa.hava.io:9700'
   # Format is a comma separated string "https://qa.hava.io:9700,https://dev.hava.io:9700"
   cors_hosts:
-  # Set to false to disable cors validation (for debugging)
-  cors_enabled: true
+  # Set to true to enable cors validation
+  cors_enabled: false
   # Sets the log level for hava, suported values: debug, info, warn, error
   log_level: info
   # This block is for enabling auth0 for managing user credentials, please reach out to hava support on details on how to configure this
@@ -59,7 +54,6 @@ web:
   # Used for performance tuning of the web instance
   concurrency: 2
   threads: 5
-  lifetime: 1440
   # AWS ALB defaults to 60, needs to be 60 + timeout value
   persistent_timeout: 75 
   # CPU and memory limits of the pods, increase to scale the pods vertically
@@ -73,30 +67,17 @@ web:
     type: AWS
     # add cert ARN here if ssl is set to true in environment
     acm_cert_arn: ""
+
 # This block configures the notify pods
 websocket:
   # Number of pods to run in parallel 
   replicas: 1
-  # Used for performance tuning of the websocket instance
-  concurrency: 2
-  pool: 5
-  threads: 2
-  lifetime: 1440
   #CPU and memory limits of the pods, increase to scale the pods vertically
   cpu: 100m
   memory: 256M
 
 # This block configures the import pods
 import:
-  # Number of pods to run in parallel
-  replicas: 1
-  # Used for performance tuning of the import pods
-  concurrency: 2
-  pool: 40
-  lifetime: 9000
-  # CPU and memory limits of the pods, increase to scale pods vertically
-  cpu: 200m
-  memory: 1536M
   # configuration for automatic synchronization of sources
   auto_sync:
     # Enables the scheduled synchronization
@@ -118,25 +99,12 @@ import:
     # aws secret key for the user (if using a user)
     secret_key: "example"
 
-# This block configures the environment build pods
-build:
-  # Number of pods to run in parallel
-  replicas: 1
-  # Used for performance tuning the build pods
-  concurrency: 2
-  lifetime: 1440
-  pool: 40
-  # CPU and memory limits of the pod, increase to scale pods vertically
-  cpu: 100m
-  memory: 1024M
-
 # This block configues the scheduler pods
 clock:
   # Number of pods to run in parallel, 1 is recommended
   replicas: 1
   # User for performance tuning
   concurrency: 2
-  lifetime: 1440
   pool: 40
   # CPU and memory limits of the pod, increase to scale pods vertically
   cpu: 100m
@@ -144,15 +112,6 @@ clock:
 
 # This block configures the environment render pods
 render:
-  # Number of pods t orun in parallel
-  replicas: 1
-  # Used for performance tuning
-  concurrency: 2
-  lifetime: 1440
-  pool: 40
-  # CPU and memory limits of the pods, increase to scale pods vertically
-  cpu: 250m
-  memory: 2048M
   # where renders will be stored when completed, supported values: s3, azure-blob
   storage_type: s3
   # prefix added to path for files stored in storage location
@@ -179,39 +138,16 @@ render:
     # secret access key used to access storage account
     access_key: ""
 
-# This block configures the background worker pods
-worker:
-  # Number of background workers to run in parallel
-  replicas: 1
-  # Used for performance tuning of the pods
-  concurrency: 2
-  lifetime: 1440
-  pool: 40
-  # CPU and memory limit of the pods, increase to scale pods vertically
-  cpu: 100m
-  memory: 1024M
 
-# This block configures the report builder pods
-report:
-  # Number of report builder to run in parallel
-  replicas: 1
-  # Used for prformance tuning of the pods
+workers:
+  # Number of pods to run in parallel, 2 is recommended for a basic deployment
+  replicas: 2
+  # used for performance tuning
   concurrency: 2
-  lifetime: 1440
   pool: 40
-  # CPU and memory limits of the pods, increase to scale vertically
-  cpu: 100m
-  memory: 1024M
-
-# This block configures the maintenance worker pods
-maintenance:
-  # Number of maintenance workers to run in parallel
-  replicas: 1
-  # Used to performance tuning of the pods
-  concurrency: 2
-  lifetime: 1440
-  pool: 40
-
+  # CPU and memory limits of the pod, increase to scale pods vertically
+  cpu: 1000m
+  memory: 2048M 
 
 database:
   # uri of the postgres server endpoint to connect to
@@ -245,6 +181,7 @@ email:
 cache:
   ## set to false if hosting redis externally. e.g. memstore paas
   enabled: true
+  storage_class: hava
 
 # persistent storage configuration
 storage:
@@ -295,7 +232,7 @@ elasticsearch:
       memory: "1536M"
   # Perstitent storage for the elastic cluster, change this to increase the storage of the cluster
   volumeClaimTemplate:
-    storageClassName: hava-ssd
+    storageClassName: hava
     resources:
       requests:
         storage: 200Gi


```

## 0.8.3 ![AppVersion: v2.2.705](https://img.shields.io/static/v1?label=AppVersion&message=v2.2.705&color=success&logo=) ![Kubernetes: >=1.19.0-0 <1.25](https://img.shields.io/static/v1?label=Kubernetes&message=%3E%3D1.19.0-0&color=informational&logo=kubernetes) ![Helm: v3](https://img.shields.io/static/v1?label=Helm&message=v3&color=informational&logo=helm)
---

* Moved database url environment variable to secret store for more secure database management


## 0.8.2 ![AppVersion: v2.2.705](https://img.shields.io/static/v1?label=AppVersion&message=v2.2.705&color=success&logo=) ![Kubernetes: >=1.19.0-0 <1.25](https://img.shields.io/static/v1?label=Kubernetes&message=%3E%3D1.19.0-0&color=informational&logo=kubernetes) ![Helm: v3](https://img.shields.io/static/v1?label=Helm&message=v3&color=informational&logo=helm)
---

* Added config value for disabling CORS validation for debug purposes

### Default Value Changes

```diff
diff --git a/charts/hava/values.yaml b/charts/hava/values.yaml
index 863ce4f..75570c7 100644
--- a/charts/hava/values.yaml
+++ b/charts/hava/values.yaml
@@ -23,6 +23,8 @@ environment:
   # This is used to add additional allowed sources for CORS, needs to be fully qualified domains including protocol and port. e.g. 'https://qa.hava.io:9700'
   # Format is a comma separated string "https://qa.hava.io:9700,https://dev.hava.io:9700"
   cors_hosts:
+  # Set to false to disable cors validation (for debugging)
+  cors_enabled: true
   # Sets the log level for hava, suported values: debug, info, warn, error
   log_level: info
   # This block is for enabling auth0 for managing user credentials, please reach out to hava support on details on how to configure this

```

## 0.8.1  ![AppVersion: v2.2.705](https://img.shields.io/static/v1?label=AppVersion&message=v2.2.705&color=success&logo=) ![Kubernetes: >=1.19.0-0 <1.25](https://img.shields.io/static/v1?label=Kubernetes&message=%3E%3D1.19.0-0&color=informational&logo=kubernetes) ![Helm: v3](https://img.shields.io/static/v1?label=Helm&message=v3&color=informational&logo=helm)
---

**Release date:** 2023-04-12

* Removed kubernetes version check from chart as it was breaking installs on EKS clusters

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