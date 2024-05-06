# Helm chart for Hava.io

![License](https://img.shields.io/github/license/teamhava/hava-helm-selfhosted)
![Release](https://img.shields.io/github/v/release/teamhava/hava-helm-selfhosted)

This chart provides an automated way to deploy and upgrade Hava.io in your own cloud environment. It's engineered to require minimal intervention and should deploy in most environments with default values.

Please review [change log](Changelog.md) for details on what has changed since previous releases

## Install

Installation guide for both AWS and Azure can be found in in our [documentation](https://developer.hava.io/self-hosted/installation-guides)

## Dependencies

This chart depends on a few other charts to deploy services in the kubernetes cluster

 - elasticsearch:6.8.13
 - Kibana:6.8.13

## Release

To release a new version of a chart, increment the chart version and a github action will create a new release and update the manifest files.