# Change Log

## 2.6.1469 ![AppVersion: v2.6.1469](https://img.shields.io/static/v1?label=AppVersion&message=v2.6.1469&color=success&logo=) ![Kubernetes: >=1.25.0-0 <1.31.0-0](https://img.shields.io/static/v1?label=Kubernetes&message=%3E%3D1.28.0-0&color=informational&logo=kubernetes) ![Helm: v3](https://img.shields.io/static/v1?label=Helm&message=v3&color=informational&logo=helm)

- Upgrade: Hava now uses Elasticsearch 7
- Feature: Better display of AWS network resources - transit gateways and VPC endpoints now display connections to relevant VPCs
- Feature: AWS Network Load Balancers now supports displaying connections for IPs in target groups
- Feature: Display connected EventBus resources in Lambda attributes
- Fix: Handle connection errors in kube imports
- Fix: Better handling for Azure DisallowedProvider errors
- Fix: Better handling of pagination for AWS VPC endpoints during import
- Fix: Better handling of nil ID's in responses from AWS and GCP
- Security: System and library patches

```diff
diff --git a/charts/hava/Chart.lock b/charts/hava/Chart.lock
index 80615ad..519b82f 100644
--- a/charts/hava/Chart.lock
+++ b/charts/hava/Chart.lock
@@ -1,9 +1,9 @@
 dependencies:
 - name: elasticsearch
-  repository: https://teamhava.github.io/hava-helm-selfhosted
-  version: 6.8.23
+  repository: https://helm.elastic.co
+  version: 7.17.3
 - name: kibana
   repository: https://helm.elastic.co
-  version: 6.8.22
-digest: sha256:cee31cb8a2a3182e021b9bfbc23cdae39530563e7ebdead00b31d0c58490b52f
-generated: "2024-01-04T16:27:30.087191+11:00"
+  version: 7.17.3
+digest: sha256:7b50aee538160f0dbcb3cc7e095c901fda0eb3d076bd3a5c7fd84a3fdd913e3b
+generated: "2025-03-13T11:07:25.422722+11:00"
diff --git a/charts/hava/Chart.yaml b/charts/hava/Chart.yaml
index 7d21c3f..a0365dd 100644
--- a/charts/hava/Chart.yaml
+++ b/charts/hava/Chart.yaml
@@ -17,13 +17,13 @@ type: application
 # This is the chart version. This version number should be incremented each time you make changes
 # to the chart and its templates, including the app version.
 # Versions are expected to follow Semantic Versioning (https://semver.org/)
-version: 2.6.1467
+version: 2.6.1469

 # This is the version number of the application being deployed. This version number should be
 # incremented each time you make changes to the application. Versions are not expected to
 # follow Semantic Versioning. They should reflect the version the application is using.
 # It is recommended to use it with quotes.
-appVersion: "2.6.1467"
+appVersion: "2.6.1469"

 kubeVersion: ">= 1.25.0-0"

diff --git a/charts/hava/values.yaml b/charts/hava/values.yaml
index 4928cbd..1f773ec 100644
--- a/charts/hava/values.yaml
+++ b/charts/hava/values.yaml
@@ -35,7 +35,7 @@ environment:
 # This block is for setting the image used by the Hava pods. Changing if a private container repository is needed
 image:
   repository: hava/self-hosted
-  tag: 2.5.1437
+  tag: 2.6.1469
   pull_policy: IfNotPresent
   # Set this to use a secret to authenticate with a private container registry
   pull_secret_name:
@@ -204,11 +204,13 @@ elasticsearch:
   # Image repository to download the elastic image from, change if using an internal container registry
   image: hava/elasticsearch
   # the tag of the image
-  imageTag: 6.8-s3-backup-1.0.6
+  imageTag: 7.17-s3-backup-1.0.7
   esConfig:
     # sets the max size of imported documents
     elasticsearch.yml: |
       http.max_content_length: 500mb
+      bootstrap.system_call_filter: false
+      xpack.security.enabled: false
   backup:
     # Change this to disable backups for the elasticsearch deployment
     enabled: true
```

## 2.5.1438 ![AppVersion: v2.5.1438](https://img.shields.io/static/v1?label=AppVersion&message=v2.5.1154&color=success&logo=) ![Kubernetes: >=1.25.0-0 <1.29.0-0](https://img.shields.io/static/v1?label=Kubernetes&message=%3E%3D1.28.0-0&color=informational&logo=kubernetes) ![Helm: v3](https://img.shields.io/static/v1?label=Helm&message=v3&color=informational&logo=helm)

- Fix: Fix azure-blob storage for embedded diagrams

```diff
diff --git a/charts/hava/templates/configmap.yaml b/charts/hava/templates/configmap.yaml
index 0d7194b..db4263d 100644
--- a/charts/hava/templates/configmap.yaml
+++ b/charts/hava/templates/configmap.yaml
@@ -26,17 +26,25 @@ data:
   CAR_USER_ACCESS_KEY: {{ .Values.import.aws.access_key | quote }}

   RENDER_STORAGE_TYPE: {{ .Values.render.storage_type | quote }}
-  RENDER_STOAGE_PREFIX: {{ .Values.render.storage_prefix | quote }}
+  RENDER_STORAGE_PREFIX: {{ .Values.render.storage_prefix | quote }}
+  EMBEDDABLE_STORAGE_TYPE: {{ .Values.render.storage_type | quote }}
+  EMBEDDABLE_STORAGE_PREFIX: {{ .Values.render.storage_prefix | quote }}
+
   {{- if eq (.Values.render.storage_type | upper) "S3"}}
   RENDER_S3_ACCESS_KEY_ID: {{ .Values.render.aws.access_key | quote }}
   RENDER_STORAGE_LOCATION: {{ .Values.render.aws.bucket | quote }}
   RENDER_STORAGE_REGION: {{ .Values.render.aws.region | quote }}
+
+  EMBEDDABLE_STORAGE_LOCATION: {{ .Values.render.aws.bucket | quote }}
+  EMBEDDABLE_STORAGE_REGION: {{ .Values.render.aws.region | quote }}
   {{- end}}

   {{- if eq (.Values.render.storage_type | upper) "AZURE-BLOB" }}
   RENDER_STORAGE_LOCATION:  {{ .Values.render.azure.blob_container | quote }}
   RENDER_AZURE_STORAGE_ACCOUNT_NAME: {{ .Values.render.azure.storage_account | quote }}
   RENDER_AZURE_STORAGE_ACCESS_KEY: {{ .Values.render.azure.access_key | quote }}
+
+  EMBEDDABLE_STORAGE_LOCATION: {{ .Values.render.azure.blob_container | quote }}
   {{- end}}

   # Application URLs
```

## 2.5.1437 ![AppVersion: v2.5.1437](https://img.shields.io/static/v1?label=AppVersion&message=v2.5.1154&color=success&logo=) ![Kubernetes: >=1.25.0-0 <1.29.0-0](https://img.shields.io/static/v1?label=Kubernetes&message=%3E%3D1.28.0-0&color=informational&logo=kubernetes) ![Helm: v3](https://img.shields.io/static/v1?label=Helm&message=v3&color=informational&logo=helm)

- Feature: SCIM provisioning is now available
- Feature: Added support for ap-southeast-5 region in AWS
- Feature: Audit logs are now enable for all licences. You can view them in Account Settings
- Fix: Fixed and issue with fonts not loading in diagrams for some setups
- Fix: Fixed imports for WAFv2 where data sent from AWS was occasionally malformed
- Fix: Better handling of multiple projects in GCP where the service account might not have read access on the projects.
- Security: System and library patches

```diff
diff --git a/charts/hava/values.yaml b/charts/hava/values.yaml
index 6dcaf37..4928cbd 100644
--- a/charts/hava/values.yaml
+++ b/charts/hava/values.yaml
@@ -35,7 +35,7 @@ environment:
 # This block is for setting the image used by the Hava pods. Changing if a private container repository is needed
 image:
   repository: hava/self-hosted
-  tag: 2.5.1408
+  tag: 2.5.1437
   pull_policy: IfNotPresent
   # Set this to use a secret to authenticate with a private container registry
   pull_secret_name:
```

## 2.5.1408 ![AppVersion: v2.5.1408](https://img.shields.io/static/v1?label=AppVersion&message=v2.5.1154&color=success&logo=) ![Kubernetes: >=1.25.0-0 <1.29.0-0](https://img.shields.io/static/v1?label=Kubernetes&message=%3E%3D1.28.0-0&color=informational&logo=kubernetes) ![Helm: v3](https://img.shields.io/static/v1?label=Helm&message=v3&color=informational&logo=helm)

- Feature: Added support for anonymous SMTP endpoints
- Feature: Added storage class for aws gp3 called hava-gp3
- Fix: Fonts and icons now load from the local host rather than a CDN
- Security: Updated latest patches for OS and libraries 

```diff
diff --git a/charts/hava/templates/configmap.yaml b/charts/hava/templates/configmap.yaml
index b787694..0d7194b 100644
--- a/charts/hava/templates/configmap.yaml
+++ b/charts/hava/templates/configmap.yaml
@@ -91,7 +91,11 @@ data:
   EMBEDDABLE_TYPE: "s3"
 
   # Email
+  {{- if .Values.email.smtp.user }}
   SMTP_USER: {{ .Values.email.smtp.user | quote }}
+  {{- else }}
+  SMTP_ANONYMOUS: "true"
+  {{- end }}
   SMTP_ADDRESS: {{ .Values.email.smtp.endpoint | quote }}
   SMTP_PORT: {{ .Values.email.smtp.port | quote }}
   SMTP_DOMAIN: {{ .Values.email.smtp.domain | quote }}
diff --git a/charts/hava/templates/deployment-clock.yaml b/charts/hava/templates/deployment-clock.yaml
index b5229d3..6d21ae3 100644
--- a/charts/hava/templates/deployment-clock.yaml
+++ b/charts/hava/templates/deployment-clock.yaml
@@ -99,11 +99,13 @@ spec:
             secretKeyRef:
               name: {{ include "hava.fullname" . }}-secrets
               key: "s3-secret-access-key"
+        {{- if .Values.email.smtp.pass }}
         - name: SMTP_PASSWORD
           valueFrom:
             secretKeyRef:
               name: {{ include "hava.fullname" . }}-secrets
               key: "smtp-email-pass"
+        {{- end }}
       volumes:
       - name: license
         secret:
diff --git a/charts/hava/templates/deployment-combined_wokers.yaml b/charts/hava/templates/deployment-combined_wokers.yaml
index ad22f8d..84d82b3 100644
--- a/charts/hava/templates/deployment-combined_wokers.yaml
+++ b/charts/hava/templates/deployment-combined_wokers.yaml
@@ -99,11 +99,13 @@ spec:
             secretKeyRef:
               name: {{ include "hava.fullname" . }}-secrets
               key: "s3-secret-access-key"
+        {{- if .Values.email.smtp.pass }}
         - name: SMTP_PASSWORD
           valueFrom:
             secretKeyRef:
               name: {{ include "hava.fullname" . }}-secrets
               key: "smtp-email-pass"
+        {{- end }}
       volumes:
       - name: license
         secret:
diff --git a/charts/hava/templates/deployment-web.yaml b/charts/hava/templates/deployment-web.yaml
index 6d05336..964b6a8 100644
--- a/charts/hava/templates/deployment-web.yaml
+++ b/charts/hava/templates/deployment-web.yaml
@@ -94,11 +94,13 @@ spec:
               secretKeyRef:
                 name: {{ include "hava.fullname" . }}-secrets
                 key: "s3-secret-access-key"
+          {{- if .Values.email.smtp.pass }}
           - name: SMTP_PASSWORD
             valueFrom:
               secretKeyRef:
                 name: {{ include "hava.fullname" . }}-secrets
                 key: "smtp-email-pass"
+          {{- end }}
           - name: SECRET_KEY
             valueFrom:
               secretKeyRef:
diff --git a/charts/hava/templates/deployment-websocket.yaml b/charts/hava/templates/deployment-websocket.yaml
index b1a7398..a0fbeb5 100644
--- a/charts/hava/templates/deployment-websocket.yaml
+++ b/charts/hava/templates/deployment-websocket.yaml
@@ -106,11 +106,6 @@ spec:
               secretKeyRef:
                 name: {{ include "hava.fullname" . }}-secrets
                 key: "s3-secret-access-key"
-          - name: SMTP_PASSWORD
-            valueFrom:
-              secretKeyRef:
-                name: {{ include "hava.fullname" . }}-secrets
-                key: "smtp-email-pass"
           - name: SECRET_KEY
             valueFrom:
               secretKeyRef:
diff --git a/charts/hava/templates/secrets.yaml b/charts/hava/templates/secrets.yaml
index c95093d..ad3aad7 100644
--- a/charts/hava/templates/secrets.yaml
+++ b/charts/hava/templates/secrets.yaml
@@ -14,7 +14,9 @@ stringData:
   web-secret-key: {{ required "Encryption web key required, please set encryption.web_key in config file." .Values.encryption.web_key | quote }}
   database-pass: {{ required "Database password needs to be set, please set database.password in the config file" .Values.database.password | quote }}
   elasticsearch-pass: {{ .Values.elasticsearch.password | quote }}
+  {{- if .Values.email.smtp.pass }}
   smtp-email-pass: {{ .Values.email.smtp.pass | quote }}
+  {{- end }}
   images-secret-key: {{ .Values.render.aws.secret_key | quote }}
   {{- if not .Values.render.aws.use_sa_role }}
   s3-secret-access-key: {{ required "An s3 secret key is required for storing environment renders. Use 'render.aws.secret_key' to set it" .Values.render.aws.secret_key | quote }}
diff --git a/charts/hava/templates/storage.yaml b/charts/hava/templates/storage.yaml
index ce24a25..41afad0 100644
--- a/charts/hava/templates/storage.yaml
+++ b/charts/hava/templates/storage.yaml
@@ -84,6 +84,17 @@ parameters:
   type: gp2
   fsType: ext4
   encrypted: "true"
+---
+apiVersion: storage.k8s.io/v1
+kind: StorageClass
+metadata:
+  name: hava-gp3
+provisioner: kubernetes.io/aws-ebs
+allowVolumeExpansion: true
+parameters:
+  type: gp3
+  fsType: ext4
+  encrypted: "true"
 {{- end }}
 
 {{- if eq (.Values.storage.type | upper)  "AZURE" }}
diff --git a/charts/hava/values.yaml b/charts/hava/values.yaml
index 1f5362f..6dcaf37 100644
--- a/charts/hava/values.yaml
+++ b/charts/hava/values.yaml
@@ -35,7 +35,7 @@ environment:
 # This block is for setting the image used by the Hava pods. Changing if a private container repository is needed
 image:
   repository: hava/self-hosted
-  tag: 2.5.1397
+  tag: 2.5.1408
   pull_policy: IfNotPresent
   # Set this to use a secret to authenticate with a private container registry
   pull_secret_name:
@@ -165,10 +165,10 @@ database:
 
 email: 
   smtp:
-    # Username for the user to authenticate with the smtp endpoint
-    user: "example"
-    # Password for the user to authenticate with the smtp endpoint
-    pass: "example"
+    # Username for the user to authenticate with the smtp endpoint, leave blank to use anonymous
+    user:
+    # Password for the user to authenticate with the smtp endpoint, leave blank to use anonymous
+    pass:
     # Port to connect to the smpt endpoint
     port: 25
     # The endpoint to connect to

```

## 2.5.1397 ![AppVersion: v2.5.1397](https://img.shields.io/static/v1?label=AppVersion&message=v2.5.1154&color=success&logo=) ![Kubernetes: >=1.25.0-0 <1.29.0-0](https://img.shields.io/static/v1?label=Kubernetes&message=%3E%3D1.28.0-0&color=informational&logo=kubernetes) ![Helm: v3](https://img.shields.io/static/v1?label=Helm&message=v3&color=informational&logo=helm)

- Security: Fixed issue where URLs could be injected to invitation emails through team names and be clickable in the email sent
- Security: System and library patches
- Fix: Several small fixes and adjustments in the UI
- Fix: Bug in data loading for embedded view that would stop loading data early for some resources

## 2.5.1393 ![AppVersion: v2.5.1393](https://img.shields.io/static/v1?label=AppVersion&message=v2.5.1154&color=success&logo=) ![Kubernetes: >=1.25.0-0 <1.29.0-0](https://img.shields.io/static/v1?label=Kubernetes&message=%3E%3D1.28.0-0&color=informational&logo=kubernetes) ![Helm: v3](https://img.shields.io/static/v1?label=Helm&message=v3&color=informational&logo=helm)

- Feature: Ruby CLI now supports cancelling all stuck jobs
- Fix: Fixed issue with Kubernetes view would in some rare cases throw errors on hover
- Fix: Updated logic for favorites and UI to match the rest of the filter 
- Fix: Fixed several small UI inconsistencies 
- Fix: Environment cards are now treated as links, so they can be opened in a new tab
- Fix: Fixed issue with regions being available for AWS Gov Cloud
- Security: Applied latest patches and security fixes for containers and libraries

## 2.5.1357 ![AppVersion: v2.5.1357](https://img.shields.io/static/v1?label=AppVersion&message=v2.5.1154&color=success&logo=) ![Kubernetes: >=1.25.0-0 <1.29.0-0](https://img.shields.io/static/v1?label=Kubernetes&message=%3E%3D1.28.0-0&color=informational&logo=kubernetes) ![Helm: v3](https://img.shields.io/static/v1?label=Helm&message=v3&color=informational&logo=helm)

- Feature: Environment filter now passed through to export
- Feature: Make SNS topic and subscription limits configurable
- Bug: Fixed issue where metadata got corrupted in some scenarios in the environments page
- Bug: Fixed authentication issue when AWS Gov Cloud is in use and us-east-1 is disabled or not available
- Bug: Fixed issue where Kubernetes sub-sources would not display properly in some search scenarios 
- Security: Latest security patches for container and libraries

## 2.5.1288 ![AppVersion: v2.5.1288](https://img.shields.io/static/v1?label=AppVersion&message=v2.5.1154&color=success&logo=) ![Kubernetes: >=1.25.0-0 <1.29.0-0](https://img.shields.io/static/v1?label=Kubernetes&message=%3E%3D1.28.0-0&color=informational&logo=kubernetes) ![Helm: v3](https://img.shields.io/static/v1?label=Helm&message=v3&color=informational&logo=helm)

- Stability: Removed CPU check for worker processes that would occasionally trigger a restart loop
- Bug: Fix missing credentials error when the AWS instance profile requests fail. Fixes some rare render failures

## 2.5.1249 ![AppVersion: v2.5.1249](https://img.shields.io/static/v1?label=AppVersion&message=v2.5.1154&color=success&logo=) ![Kubernetes: >=1.25.0-0 <1.29.0-0](https://img.shields.io/static/v1?label=Kubernetes&message=%3E%3D1.28.0-0&color=informational&logo=kubernetes) ![Helm: v3](https://img.shields.io/static/v1?label=Helm&message=v3&color=informational&logo=helm)

- Fix: Change executing user in Hava docker image to use a uid to support K8s security context settings
- Fix: Fixed warning in UI that would complain about variables not set
- Fix: Fixed issue that would some times cause an internal server error when setting Alerts recipients
- Security: Patches and vulnerability fixes
- Security: Added `securityContext.runAsNonRoot: true` to all deployments

```diff
diff --git a/charts/hava/templates/deployment-clock.yaml b/charts/hava/templates/deployment-clock.yaml
index 20ea941..b5229d3 100644
--- a/charts/hava/templates/deployment-clock.yaml
+++ b/charts/hava/templates/deployment-clock.yaml
@@ -30,6 +30,8 @@ spec:
         app.kubernetes.io/name: {{ include "hava.name" . }}-clock
         app.kubernetes.io/instance: {{ .Release.Name }}
     spec:
+      securityContext:
+        runAsNonRoot: true
       {{- if .Values.serviceaccount.enabled }}
       serviceAccountName: {{ include "hava.serviceaccount.name" . }}
       {{- end }}
diff --git a/charts/hava/templates/deployment-combined_wokers.yaml b/charts/hava/templates/deployment-combined_wokers.yaml
index 91b4df9..ad22f8d 100644
--- a/charts/hava/templates/deployment-combined_wokers.yaml
+++ b/charts/hava/templates/deployment-combined_wokers.yaml
@@ -30,6 +30,8 @@ spec:
         app.kubernetes.io/name: {{ include "hava.name" . }}-workers
         app.kubernetes.io/instance: {{ .Release.Name }}
     spec:
+      securityContext:
+        runAsNonRoot: true
       {{- if .Values.serviceaccount.enabled }}
       serviceAccountName: {{ include "hava.serviceaccount.name" . }}
       {{- end }}
diff --git a/charts/hava/templates/deployment-web.yaml b/charts/hava/templates/deployment-web.yaml
index 35efe20..6d05336 100644
--- a/charts/hava/templates/deployment-web.yaml
+++ b/charts/hava/templates/deployment-web.yaml
@@ -23,6 +23,8 @@ spec:
         app.kubernetes.io/name: {{ include "hava.name" . }}-web
         app.kubernetes.io/instance: {{ .Release.Name }}
     spec:
+      securityContext:
+        runAsNonRoot: true
       {{- if .Values.serviceaccount.enabled }}
       serviceAccountName: {{ include "hava.serviceaccount.name" . }}
       {{- end }}
diff --git a/charts/hava/templates/deployment-websocket.yaml b/charts/hava/templates/deployment-websocket.yaml
index f0c6d2e..b1a7398 100644
--- a/charts/hava/templates/deployment-websocket.yaml
+++ b/charts/hava/templates/deployment-websocket.yaml
@@ -23,6 +23,8 @@ spec:
         app.kubernetes.io/name: {{ include "hava.name" . }}-websocket
         app.kubernetes.io/instance: {{ .Release.Name }}
     spec:
+      securityContext:
+        runAsNonRoot: true
       {{- if .Values.serviceaccount.enabled }}
       serviceAccountName: {{ include "hava.serviceaccount.name" . }}
       {{- end }}
```

## 2.5.1232 ![AppVersion: v2.5.1232](https://img.shields.io/static/v1?label=AppVersion&message=v2.5.1154&color=success&logo=) ![Kubernetes: >=1.25.0-0 <1.29.0-0](https://img.shields.io/static/v1?label=Kubernetes&message=%3E%3D1.28.0-0&color=informational&logo=kubernetes) ![Helm: v3](https://img.shields.io/static/v1?label=Helm&message=v3&color=informational&logo=helm)

- Feature: Custom Connections
- Security: Security patches for containers and updated libraries
- Bug: Fixed issue where certs where not trusted when a private CA is used
- Bug: Fixed issues with the filter feature in the infrastructure view
- Bug: Fixed issue with Audit logs crashing under certain rare cases

```diff
diff --git a/charts/hava/values.yaml b/charts/hava/values.yaml
index 7cc4913..8606241 100644
--- a/charts/hava/values.yaml
+++ b/charts/hava/values.yaml
@@ -35,7 +35,7 @@ environment:
 # This block is for setting the image used by the Hava pods. Changing if a private container repository is needed
 image:
   repository: hava/self-hosted
-  tag: 2.5.1188
+  tag: 2.5.1232
   pull_policy: IfNotPresent
   # Set this to use a secret to authenticate with a private container registry
   pull_secret_name:
```

## 2.5.1189 ![AppVersion: v2.5.1188](https://img.shields.io/static/v1?label=AppVersion&message=v2.5.1154&color=success&logo=) ![Kubernetes: >=1.25.0-0 <1.29.0-0](https://img.shields.io/static/v1?label=Kubernetes&message=%3E%3D1.28.0-0&color=informational&logo=kubernetes) ![Helm: v3](https://img.shields.io/static/v1?label=Helm&message=v3&color=informational&logo=helm)

- Fixed heml template issue where the redis image couldn't be updated from values file

```diff
diff --git a/charts/hava/values.yaml b/charts/hava/values.yaml
index 48a9f58..7cc4913 100644
--- a/charts/hava/values.yaml
+++ b/charts/hava/values.yaml
@@ -184,6 +184,9 @@ cache:
   ## set to false if hosting redis externally. e.g. memstore paas
   enabled: true
   storage_class: hava
+  image:
+    name: redis
+    tag: 4.0-alpine
 
 # persistent storage configuration
 ```

## 2.5.1188 ![AppVersion: v2.5.1188](https://img.shields.io/static/v1?label=AppVersion&message=v2.5.1154&color=success&logo=) ![Kubernetes: >=1.25.0-0 <1.29.0-0](https://img.shields.io/static/v1?label=Kubernetes&message=%3E%3D1.28.0-0&color=informational&logo=kubernetes) ![Helm: v3](https://img.shields.io/static/v1?label=Helm&message=v3&color=informational&logo=helm)

- Fixed config ussue that caused captcha to load when not expected to

## 2.5.1154 ![AppVersion: v2.5.1154](https://img.shields.io/static/v1?label=AppVersion&message=v2.5.1154&color=success&logo=) ![Kubernetes: >=1.25.0-0 <1.29.0-0](https://img.shields.io/static/v1?label=Kubernetes&message=%3E%3D1.28.0-0&color=informational&logo=kubernetes) ![Helm: v3](https://img.shields.io/static/v1?label=Helm&message=v3&color=informational&logo=helm)

- Feature: Environment Notes
- Feature: Environment Filters
- Feature: Account Audit Logs
- Feature: New UI
- Feature: API Endpoint to list import jobs and their logs
- Performance: Upgraded libraries to improve API performance
- Performance: Improved import speeds for accounts with large amounts of SNS topics
- Improvement: Improved search stability, suggestions, and performance
- Improvement: View type is updated in environment URLs to assist in linking
- Improvement: Removed defunct AWS icon set
- Bug: Fixed issues with missing data in GCP imports and pricing
- Bug: Fixed a pagination bug that could sometimes result in duplicate versions being listed for an environment
- Security: Updated dependencies with latest security patches

```diff
diff --git a/charts/hava/values.yaml b/charts/hava/values.yaml
index dbfc449..729fdcb 100644
--- a/charts/hava/values.yaml
+++ b/charts/hava/values.yaml
@@ -35,7 +35,7 @@ environment:
 # This block is for setting the image used by the Hava pods. Changing if a private container repository is needed
 image:
   repository: hava/self-hosted
-  tag: 2.5.958
+  tag: 2.5.1154
   pull_policy: IfNotPresent
   # Set this to use a secret to authenticate with a private container registry
   pull_secret_name:
```

## 1.3.0 ![AppVersion: v2.5.958](https://img.shields.io/static/v1?label=AppVersion&message=v2.5.958&color=success&logo=) ![Kubernetes: >=1.25.0-0 <1.28.0-0](https://img.shields.io/static/v1?label=Kubernetes&message=%3E%3D1.19.0-0&color=informational&logo=kubernetes) ![Helm: v3](https://img.shields.io/static/v1?label=Helm&message=v3&color=informational&logo=helm)

- Feature: AWS EventBridge support
- Feature: AWS API Gateway V2 support
- Feature: AWS Appsync support
- Feature: AWS OpenSearch support
- Feature: AWS RDS DB Proxy support
- Feature: AWS Firehose and Kinesis support
- Feature: AWS MSK support
- Feature: AWS DocDB support
- Feature: AWS Gateway Load Balancer support
- Feature: Improved AWS Transit Gateway support including peers, connections, and multicast domains
- Feature: Add ca-west-1 region for AWS
- Feature: Display a link to the AWS console for supported resource types
- Feature: Display account ID and source on attributes for AWS resources
- Improvement: increased stability for Kubernetes imports
- Improvement: better error handling and timeout handling for SSO
- Improvement: Allow regex matching for CORS configuration
- Improvement: Update AWS icons to latest versions
- Improvement: AWS Glacier vaults are now searchable with tags
- Improvement: Add resource highlighting when hovering over Azure security groups and route tables
- Improvement: Add Source button on the source list
- Improvement: Add loading indicator and logic to viewer revisions and diff
- Performance: upgraded to Ruby 3 with YJIT enabled to improve performance
- Performance: large improvement to environment and search load times
- Security: upgraded build image and libraries
- Security: added config to enable secure cookies
- Bug: Fix issues with too many credential requests when using instance profiles in self hosted
- Bug: Improve handling of invalid API tokens
- Bug: Fix writing to local drive when using docker compose in self hosted
- Bug: Fix error loading Glacier Vaults when they don’t have an SNS connection
- Bug: Remove checkbox from ‘timestamp’ toggle in Safari
- Bug: Handle open search dependency failures when the nodes haven't spun up yet
- Bug: Fix a range of possible nil values in diagram connections
- Bug: Fix lambda imports for layers with huge ARNs
- Bug: Fix pricing issue with GCP SQL Instances
- Bug: Catch validation exceptions in alert creation
- Bug: Connections no longer get disconnected when selecting a non-displayed resource
- Bug: Report an error when trying to do a search without providing a query string

```diff
diff --git a/charts/hava/values.yaml b/charts/hava/values.yaml
index 94c034a..99649a8 100644
--- a/charts/hava/values.yaml
+++ b/charts/hava/values.yaml
@@ -22,6 +22,8 @@ environment:
   cors_enabled: false
   # Sets the log level for hava, suported values: debug, info, warn, error
   log_level: info
+  # Sets up an http proxy for Hava to use when connecting to cloud providers to access the public APIs
+  proxy:
   # This block is for enabling auth0 for managing user credentials, please reach out to hava support on details on how to configure this
   auth0:
     enabled:
```

## 1.2.0 ![AppVersion: v2.3.2686](https://img.shields.io/static/v1?label=AppVersion&message=v2.2.705&color=success&logo=) ![Kubernetes: >=1.25.0-0 <1.28.0-0](https://img.shields.io/static/v1?label=Kubernetes&message=%3E%3D1.19.0-0&color=informational&logo=kubernetes) ![Helm: v3](https://img.shields.io/static/v1?label=Helm&message=v3&color=informational&logo=helm)

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