# guestbook
A simple Kube uService-based application that uses a MongoDB client written in Java running on JBoss EAP6.

## Overview
The guestbook is a simple client/server application that consists of two kube
services. The client service is backed by a pod which is written in Java
and runs on a JBoss EAP6 server. It provides the web front end and interacts
with the mongodb database.  The server service is backed by a mongod pod.  The
client discovers the mongod pod via environment variables which are based on
kube label selectors defined by the services.

This example assumes you have a working kube cluster.

## Node Configuration
On each kube node, edit `/etc/sysconfig/docker` and configure presto.haveopen.com as an INSECURE_REGISTRY.

Here is another automated way of doing so using `sed`.

`$ sudo sed -i.bak -e 's/^INSECURE_REGISTRY/#INSECURE_REGISTRY/' -e '/^# INSECURE_REGISTRY/a INSECURE_REGISTRY=--insecure-registry presto.haveopen.com:5000' /etc/sysconfig/docker`

`$ sudo systemctl restart docker`

### Create the replication controllers and services.
    $ kubectl create -f mongod-rc.yaml
    $ kubectl expose rc mongodb
    $ kubectl create -f mongo-client-rc.yaml
    $ kubectl expose rc mongo-client

### Verify both pods are running.
`$ kubectl get pods`

### Verify the services are exposed.
` $ kubectl get services`

#### To test mongod from the master:

    $ curl http://<mongod-service-ip>:27017

#### If the mongod service is working, the following message will be returned:

    You are trying to access MongoDB on the native driver port. For http 
    diagnostic access, add 1000 to the port number.

#### From the master, visit the client.

     $ lynx http://<mongo_client-service-ip>:8080/MongoDBWebapp

#### In case you only have curl. You'll need ther session url.

     $ curl --data "name=Bob&country=USA" http://<mongo_client-service-ip>:8080/MongoDBWebapp/addPerson;jsessi-dHmJlrYDBOm7TIRFQ-U-

## Perform the following checks from the host desktop.

### Determine which IP:port to connect to from the KUBE-PORTALS-HOST chain.

    $ sudo iptables -L -t nat

    $ firefox http://<ip>:<port>/MongoDBWebapp

#### Resize the front end.

    $ kubectl scale --replicas=2 rc mongo-client

    $ kubectl get endpoints
