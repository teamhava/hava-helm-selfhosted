# HELM chart for Hava.io

![License](https://img.shields.io/github/license/teamhava/hava-helm-selfhosted)
![Release](https://img.shields.io/github/v/release/teamhava/hava-helm-selfhosted)

This chart provides an automated way to deploy and upgrade Hava.io in your own AWS environment

A detailed step by step install and configuration guides can be found in our [docs](https://docs.hava.io) #todo add direct link to guide

This file gives a high level overview of the chart and how to use it only.

## Install

### Pre-Install steps

The chart expects a few servies to be deployed and configured in your environment before you deploy. 

- Kubernetes cluster
- PostgreSQL
- S3 Bucket for Elasticsearch backup
- S3 Bucket for storage of renders and reports

For a detailed guide on setting these up, please see our [docs](https://docs.hava.io) #todo add direct link to guide

### HELM Install

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
  license_email: admin@hava.io #replace with the email in your license

environment:
  domain: "example.com"

database:
  host: "localhost"
  port: 5432
  user: "postgres"
  password: "example"

email: 
  smtp:
    user: "example"
    pass: "example"
    port: 53
    domain: "example.com"
    address: "example.com"
    from_address: "noreply@example.com"
    from_name: "No Reply"

render:
   aws:
    access_key: "example"
    bucket: "example"
    region: "example"
    secret_key: "example"

import:
  azure:
    client_id: "example"
    subscription_id: "example"
    tenant_id: "example"
    secret_key: "example"
  aws:
    account_id: "example"
    access_key: "example"
    secret_key: "example"

web:
  secret_key: "example"
  icons:
    scheme: http
    host: app.example.com
    port: 80 
    path: /tmp

assets:
  cdn: "app.example.com"

elasticsearch:
  backup_bucket_name: hava-elasticsearch-backup
```

# Dependencies

This chart depends on a few other charts to deploy services in the kubernetes cluster

 - elasticsearch:6.8.13
 - Kibana:6.8.13

# Release

To release a new version of a chart, increment the chart version and a github action will create a new release and update the manifest files.