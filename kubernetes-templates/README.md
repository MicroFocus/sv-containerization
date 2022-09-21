# Running SV Server in Kubernetes

This readme describes how to setup SV Server and other required components for a complete working environment in Kubernetes. The steps are valid for SV version 2022 R1. These examples are meant to be used as a starting point for your own configuration and are not reflecting production ready configuration.

## Setup

The working environment can be created using following steps:

1. `kubectl apply -f apls.yml`

   This command creates an AutoPass License Server service `apls-svc`. After creation, you will need to login at https://apls-svc:5814/autopass/ with admin/password and upload the license. If you don't provide a license, you'll not be able to switch any virtual service to simulation.

1. Replace `kubernetes-master` in the svserver.yml with the node name, where you want to host the SV Server and SV Management.

1. Pre-create paths on the node, where you want to host SV Server and SV Management containers:

   `sudo mkdir /mnt/sv/logs`

   `sudo mkdir /mnt/sv/work`

   `sudo mkdir /mnt/postgres`


1. `kubectl apply -f svserver.yml`

This command creates a Deployment, which consists from following containers:
   - virtualization/sv-server - SV Server exposed at https://sv-server-svc:6085/api (user admin/password)
   - virtualization/sv-svm - SV Management exposed at https://sv-server-svc:6086 (user admin/password)
   - postgres - PostgreSQL database is used by sv-server and is not exposed via service

Please note that SV Server needs to run with specific configuration of securityContext if you don't want it to run as root. This means you need to use `fsGroup: 1234` and optionally `runAsUser: 1234` in the securityContext. To let SV Server access files on volumes when it runs under UID 1234, all Persistent Volumes must support propagation of securityContext configuration. `hostPath` volumes do not support securityContext propagation and that's why `local` volumes are used in the example.

## How to Add new User

If you want to add additional users potentionally with different permissions, you can use `sv-AddUser` script in the sv-server container to do so. For example:

`kubectl exec sv-server-5fb876f899-zk2mh -- /opt/microfocus/sv-server/bin/sv-AddUser "mynewuser" "mynewpassword" "SVM Users,SV Operators,SV Publishers,SV Runtime Administrators,SV Server Administrators"`
