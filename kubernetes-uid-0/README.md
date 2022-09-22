# Running SV Server in Kubernetes under root

This example shows how to setup all the components required to run SV Server. The user under which SV Server runs is a root user with UID 0, which is not recommended due to security reasons, however the setup is simpler and might be useful when you would like just to play with it. The steps are valid for SV version 2022 R1. These examples are meant to be used as a starting point for your own configuration and are not reflecting production ready configuration.

## Setup

The working environment can be created using following steps:

1. `kubectl apply -f apls.yml`

   This command creates an AutoPass License Server service `apls-svc`. After creation, you will need to login at https://apls-svc:5814/autopass/ with admin/password and upload the license. If you don't provide a license, you'll not be able to switch any virtual service to simulation.

1. `kubectl apply -f svserver.yml`

This command creates a Deployment, which consists from following containers:
   - virtualization/sv-server - SV Server exposed at https://sv-server-svc:6085/api (user admin/password)
   - virtualization/sv-svm - SV Management exposed at https://sv-server-svc:6086 (user admin/password)
   - postgres - PostgreSQL database is used by sv-server and is not exposed via service

## How to Add new User

If you want to add additional users potentionally with different permissions, you can use `sv-AddUser` script in the sv-server container to do so. For example:

`kubectl exec sv-server-5fb876f899-zk2mh -- /opt/microfocus/sv-server/bin/sv-AddUser "mynewuser" "mynewpassword" "SVM Users,SV Operators,SV Publishers,SV Runtime Administrators,SV Server Administrators"`
