# Helm chart for Hava.io

![License](https://img.shields.io/github/license/teamhava/hava-helm-selfhosted)
![Release](https://img.shields.io/github/v/release/teamhava/hava-helm-selfhosted)

This chart provides an automated way to deploy and upgrade Hava.io in your own AWS environment

> :warning: This chart is in alpha state and in active development. It is not ready for use in production

A detailed step by step install and configuration guides can be found in our [docs](https://developer.hava.io/self-hosted/installation-guides/ha-kubernetes-eks)

This file gives a high level overview of the chart and how to use it only.

## Install

### Pre-Install steps

The chart expects a few servies to be deployed and configured in your environment before you deploy. 

- Kubernetes cluster
- PostgreSQL
- S3 Bucket for Elasticsearch backup
- S3 Bucket for storage of renders and reports

For a detailed guide on setting these up, please see our [docs](https://developer.hava.io/self-hosted/installation-guides/ha-kubernetes-eks)

### Helm Install

To install Hava in your own environment you need a commercial license and a configuration file (see example bellow).
If you don't have a license, please reach out to us on https://www.hava.io to get a license.

```sh
helm repo add hava https://teamhava.github.io/hava-helm-selfhosted
helm install <your release name> hava/hava --set-file license=<path to licensefile> -f <path to values configuration file>
```

## example configuration file

```yaml
setup:
  license_user: hava
  license_email: admin@example.com #replace with the email in your license

environment:
  domain: "example.com" #replace with your domain
  ## uncomment and change to use different subdomain than the defaults
  # web_subdomain: "app"
  # api_subdomain: "api"
  # websocket_subdomain: "notify"

database:
  host: "" # insert postgres endpoint url
  port: 5432 # update if port is different from default
  user: "" # insert the master user for the RDS instance
  password: "" # insert the master password for the RDS instance

email: 
  smtp: # see Email section in the configuration docs
    user: ""
    pass: ""
    port: 25
    domain: ""
    endpoint: ""
    from_address: "noreply@example.com"
    from_name: "No Reply"

render:
   aws: # see the S3 buckets optional guide for help with setting up the bucket and user
    access_key: "" # insert the access key id for a user with access to the cache s3 bucket
    secret_key: "" # insert the secret key for the user with access to the cache s3 bucket
    bucket: "" # update with the name of the cache bucket
    region: "" # update with the region the cache bucket is deployed to

import:
  aws: # credentials for the user that is used for cross account access in AWS
    account_id: ""
    access_key: ""
    secret_key: ""

elasticsearch:
  backup:
    access_key: "" # insert the access key id for the user with access to the cache s3 bucket
    secret_key: "" # insert the secret key for the user with access to the cache s3 bucket
    bucket_name: "" # insert the name of your s3 backup bucket for Elastic search

encryption:
  ## used to encrypt values stored in the database
  iv: # openssl rand -hex 8
  key: # openssl rand -hex 48

  ## used to encrypt cookies
  web_key: # openssl rand -hex 48
```

# Dependencies

This chart depends on a few other charts to deploy services in the kubernetes cluster

 - elasticsearch:6.8.13
 - Kibana:6.8.13

# Release

To release a new version of a chart, increment the chart version and a github action will create a new release and update the manifest files.