# Drools + Quarkus Hello World

## Installing and Running

- Prerequisites: install qs-playground 8.0-SNAPSHOT

- Compile and Run

    ```
     mvn clean compile quarkus:dev    
    ```

- Native Image (requires JAVA_HOME to point to a valid GraalVM)

    ```
    mvn package -Pnative
    ```
  
  native executable (and runnable jar) generated in `target/`

## Examples

### Hello World

Point to http://localhost:8080/hello for Drools hello world

### More complex example

- post 

```sh
curl -d '{"name":"edo", "age":32}' -H "Content-Type: application/json" \
    -X POST http://localhost:8080/persons                                                                                                    
```

- http://localhost:8080/persons/all returns a list of all persons

- http://localhost:8080/persons/adults returns a list of all adults

### Invoke autogenerated REST endpoints for queries

```sh
curl -X POST -H 'Accept: application/json' -H 'Content-Type: application/json' -d '{"adultAge":18,"persons":[{"name":"Mario","age":45,"adult":false},{"name":"Marilena","age":47,"adult":false},{"name":"Sofia","age":7,"adult":false}]}' http://localhost:8080/FindAdults
```
```sh
curl -X POST -H 'Accept: application/json' -H 'Content-Type: application/json' -d '{"adultAge":18,"persons":[{"name":"Mario","age":45,"adult":false},{"name":"Marilena","age":47,"adult":false},{"name":"Sofia","age":7,"adult":false}]}' http://localhost:8080/FindAdultNames
```
```sh
curl -X POST -H 'Accept: application/json' -H 'Content-Type: application/json' -d '{"adultAge":18,"persons":[{"name":"Mario","age":45,"adult":false},{"name":"Marilena","age":47,"adult":false},{"name":"Sofia","age":7,"adult":false}]}' http://localhost:8080/FindNotAdultNamesAndAge
```

### Manual Deploy on Openshift

#### Build Container on docker
```sh
docker build -t quickstarter/quick-drools .
docker images | grep quick-drools
```

#### Deploy on Openshift
By default will be created under project called "My Project"
```sh
kubectl create -f kubernetes/deployment.yml 
kubectl create -f kubernetes/service.yml 
oc expose service quick-drools
```
this create a yaml file and the route for us on openshift, like this (in routes section on My Project)
 ```yaml
 
 apiVersion: route.openshift.io/v1
 kind: Route
 metadata:
   annotations:
     openshift.io/host.generated: 'true'
   creationTimestamp: '2019-02-20T10:25:59Z'
   labels:
     app: quick-drools
   name: quick-drools
   namespace: myproject
   resourceVersion: '30743'
   selfLink: /apis/route.openshift.io/v1/namespaces/myproject/routes/quick-drools
   uid: ea2676d6-34f9-11e9-bd97-08002709a920
 spec:
   host: quick-drools-myproject.192.168.99.109.nip.io
   port:
     targetPort: http
   to:
     kind: Service
     name: quick-drools
     weight: 100
   wildcardPolicy: None
 status:
   ingress:
     - conditions:
         - lastTransitionTime: '2019-02-20T10:25:59Z'
           status: 'True'
           type: Admitted
       host: quick-drools-myproject.192.168.99.109.nip.io
       routerName: router
       wildcardPolicy: None

 ```
 ```
 oc get route
 
  NAME           HOST/PORT                                      PATH      SERVICES       PORT      TERMINATION   WILDCARD
  quick-drools   quick-drools-myproject.192.168.99.109.nip.io             quick-drools   http                    None
  ```
  
  Your address will be
  http://quick-drools-myproject.192.168.99.109.nip.io/hello