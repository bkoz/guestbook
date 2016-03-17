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
On each kube node, edit `/etc/sysconfig/docker` so my docker registry can be used to pull the mongodbwebapp container.

`ADD_REGISTRY='--add-registry registry.access.redhat.com --add-registry presto.haveopen.com:5000'`

`INSECURE_REGISTRY="--insecure-registry 172.30.0.0/16 --insecure-registry presto.haveopen.com:5000"`

`$ sudo systemctl restart docker`

## Master Configuration
### Clone this repo to your Kube Master or where every your kubectl client is running so you have access to the *.yaml files.

### Create the mongod replication controller and service.
    $ sudo kubectl create -f mongod-rc.yaml
    $ sudo kubectl expose rc mongodb

### Once the pod is running, start up the mongo-client rc and service.
    $ sudo kubectl create -f mongo-client-rc.yaml
    $ sudo kubectl expose rc mongo-client

### Verify both pods are running and the services are exposed.
    $ sudo kubectl get pods
    $ sudo kubectl get services

### To test mongod from the master:

    $ curl http://<mongodb-service-ip>:27017

### If the mongod service is working, the following message will be returned:

    You are trying to access MongoDB on the native driver port. For http 
    diagnostic access, add 1000 to the port number.

### Visit the client and add a person to the database.

     $ lynx http://<mongo-client-service-ip>:8080/MongoDBWebapp

### In case you only have curl.

`$ curl --data "name=Bob&country=USA" http://<mongo_client-service-ip>:8080/MongoDBWebapp/addPerson;jsessi-dHmJlrYDBOm7TIRFQ-U-`

## Perform the following checks from the host desktop.
### Connect to the EAP admin console and deploy the MongoDBWebapp.war file.

    $ firefox http://<external-ip-of-mongo-client-service>:8080/MongoDBWebapp

### Resize the front end.

    $ sudo kubectl scale --replicas=2 rc mongo-client
    $ sudo kubectl get endpoints
