# Running SV Server in OpenShift Example

This readme describes how to setup SV Server and other required components for a complete working environment in OpenShift. The steps are valid for SV version 5.4.1.

## Following services will be created

All services consist of one or more PersistentVolumeClaims, DeploymentConfig and a Service resources.

- *postgres-svc* - PostgreSQL database, which is used by sv-server-svc service
- *apls-svc* - AutoPass license server, which is used by sv-server-svc service
- *sv-server-svc* - SV Server service
- *sv-svm-svc* - SV SVM service

## Setup
1. Download and setup [RedHat Openshift 4.7 CodeReady Containers](https://console.redhat.com/openshift/create/local).
1. Login

    > `oc login --token=%YOUR_TOKEN% --server=https://api.crc.testing:6443`

1. Create new SV project

    > `oc new-project sv-server`

1. Create all the resources using provided YAML configuration

    > `oc create sv-openshift-template-complete.yaml`
    
1. You can check services were correctly created by running

    > `oc get svc`
    
    You should see following services:
    
    > `apls-svc, postgres-svc, sv-server-svc, sv-svm-svc`

Note: In case you have problems pulling the images, you might need to provide a name server for your cluster this can be done by running for example: `crc start -n 8.8.8.8`

## Exposing services outside of the cluster

To consume SV Server or SVM outside of the cluster, you can do following steps:

1. Switch to sv-server project

    > `oc project sv-server`


1. Expose sv-server-svc

    > `oc expose svc sv-server-svc`


1. Check what is the host to use

    > `oc get route`

1. Check the port

    > `oc get svc`

1. You can try accessing SV Server from outside of the cluster on URL, which should look similar to this

    > `https://sv-server-svc-sv-server.apps-crc.testing:31696/api`