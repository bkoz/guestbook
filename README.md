# guestbook
Simple Kube uService example that uses Java JBoss EAP/MongoDB

## Node Configuration

On each kube node, edit /etc/sysconfig/docker and add
presto.haveopen.com:5000 as an insecure registry then 
restart docker.

`INSECURE_REGISTRY='--insecure-registry presto.haveopen.com:5000'`
 
`# systemctl restart docker`

Edit mongo-client-service.json and mongod-service.json to reflect the IP address of the nodes which will host
the services.

## Master Configuration

Start the services and replication controllers.

`$ sudo kubectl create -f mongo-client-service.json`

`$ sudo kubectl create -f mongod-service.json`

`$ sudo kubectl create -f mongo-client-rc.json`

`$ sudo kubectl create -f mongod-rc.json`

Verify the endpoints and services are working.

`$ sudo kubectl get endpoints

NAME            ENDPOINTS
kubernetes      192.168.100.203:6443
kubernetes-ro   192.168.100.203:7080
mongo-client    18.0.92.2:8080
mongod          18.0.29.2:27017

$ `

$ curl http://18.0.29.2:27017
You are trying to access MongoDB on the native driver port. For http diagnostic access, add 1000 to the port number
$ 

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

Curl the IP:Port of each service and verify the same info as above is returned.

## From the host desktop

5) Connect to the EAP console and deploy the MongoDBWebapp.war file.

$ http://192.168.100.202:9990
login: admin
password: p@ssw0rd

6) Visit the application

$ firefox http://192.168.100.201:8080/MongoDBWebapp

7) How to resize the rc's

$ sudo kubectl resize --replicas=2 rc mongod-controller
$ sudo kubectl resize --replicas=2 rc mongo-client-controller

