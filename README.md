# guestbook
A simple Kube uService based application that uses Java, JBoss EAP and MongoDB.

This example assumes you have a working kube cluster.

## Node Configuration
On each kube node, edit `/etc/sysconfig/docker` and add
`presto.haveopen.com:5000` as an insecure registry then 
restart docker.

`INSECURE_REGISTRY='--insecure-registry presto.haveopen.com:5000'`
 
`$ sudo systemctl restart docker`

## Master Configuration
Copy all of the `*.json` files to the master.

Edit `mongo-client-service.json` and `mongod-service.json` to reflect 
the IP address of the nodes which will host the services.

### Create the services and replication controllers.
    $ sudo kubectl create -f mongo-client-service.json
    $ sudo kubectl create -f mongod-service.json
    $ sudo kubectl create -f mongo-client-rc.json
    $ sudo kubectl create -f mongod-rc.json

### Verify the endpoints and services are working.
    $ sudo kubectl get endpoints
    NAME            ENDPOINTS
    kubernetes      192.168.100.203:6443
    kubernetes-ro   192.168.100.203:7080
    mongo-client    18.0.92.2:8080
    mongod          18.0.29.2:27017

From the master, curl the mongod server.

    $ curl http://18.0.29.2:27017

If the mongod service is working, the following message will be returned:

`You are trying to access MongoDB on the native driver port. For http diagnostic access, add 1000 to the port number.`

From the master, curl the EAP server.

     $ curl http://18.0.92.2:8080
    
    <!DOCTYPE html>
    <html>
    <head>
    <title>EAP 6</title>
    ...
    ...
    ...
    </body >
    </html>

    $ sudo kubectl get services

From the master, curl each service and verify the same info as 
above is returned.

## Perform the following checks from the host desktop.
Connect to the EAP console and deploy the MongoDBWebapp.war file.

    $ http://192.168.100.202:9990

    login: admin
    password: p@ssw0rd

Visit the application from a web browser.

    $ firefox http://192.168.100.201:8080/MongoDBWebapp

Resize the replication controllers.

    $ sudo kubectl resize --replicas=2 rc mongod-controller
    $ sudo kubectl resize --replicas=2 rc mongo-client-controller


