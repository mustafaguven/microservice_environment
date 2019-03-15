# Microservice Environment

Microservice architecture has been built on Spring Cloud, both Java and Kotlin supported. 



# Ports

 Application  | Port             | Language
------------- | ----------------- | -----------
Eureka Server  | 8761 ~   | Java
Config Server  | 8888 ~    | Java
Hystrix Dashboard | 7979 ~ (management: 7980)   | Java
Turbine  | 9999 ~  | Java
Zuul API Proxy  | 8400 ~  | Java
Auth Center  | 8081 ~  | Java
Catalog Service  | 8800 ~  | Kotlin
Payment Service  | 8900 ~  | Java
Order Service  | 9010 ~  | Kotlin
Product Service  | 9000 ~  | Kotlin
FrontEnd Service  | 14000 ~  | Java

# Additional Libraries

 Application  | Language
------------- | -----------
JwtCommon  | Java
CommandGateway - EventBus Manager  | Kotlin

jwtcommon (spring security common lib) is used by zuul-api and auth-center (https://github.com/mustafaguven/jwtcommon)

event-handler-service, a tiny library written in kotlin that is used by microservices. Acts as single source of truth so it holds a simple config infrastructure that allows event sourcing and cqrs mechanism. (in this particular case mongo is used as the data source) (https://github.com/mustafaguven/event-handler-service)

* all additional libraries must be installed into local repo before using otherwise microservices will not be compiled (mvn clean install)

# Notes
                
1. The config server should be started before eureka server due to the configured environment which has been set as config-first.    
2. Pom files of the client services work by the parent module (microservice_parent_pom). Please notice that to make successfull compilation microservice_parent_pom must be installed into localrepo. To do this run below command. 
```
$ maven clean install
```
3. Rabbitmq is chosen as the selected mq for this project and named "mg" user will be set as default user once docker-compose.yml started
4. None client microservices be able to work until config-server gets run successfully
5. Run below command in order to start a service with multiple instances. Eg: run product service with 3 instances
```
docker-compose up --scale product-service=3 -d
```
![](https://s3-eu-west-1.amazonaws.com/videotestpoc/Screen+Shot+2019-02-19+at+16.15.22.png)

6. Use microservice-starter for Java projects and kotlin-starter for Kotlin projects. 
```
service pom >> kotlin pom (for kotlin projects) >> java pom >> spring boot pom
```
7. Used nginx to benefit from reverse proxy configuration while scaling up the api gateway. Nginx additional conf files are in conf.d folder.  

8. Run below command to refresh spring cloud bus
```
http://localhost:8400/actuator/bus-refresh
```
9. While generating docker image, version argument can be given as parameter from the outside. If no arguments are given default version will be set as 1.0.0.
```
 docker build --build-arg version=1.5.3 -f Dockerfile -t turbine .
```
or
```
docker rmi auth-center || mvn clean install -DskipTests && docker build -f Dockerfile -t auth-center .
```
10. To use turbine through docker give below link as streamer
```
 turbine:9999/turbine.stream
```
![]()
[![](https://s3-eu-west-1.amazonaws.com/videotestpoc/hystrix-turbine.gif)](https://s3-eu-west-1.amazonaws.com/videotestpoc/turbine.mov)

11. Zuul API and Auth-center demands attended roles for every user due to authorization issues. During the first time of mysql running make sure that you execute all the commands which listed in sql authorization file.

12. To enter mysql cli, use below commands
```
docker exec -it mysql sh
.
.
(having entered into the container shell)
mysql -uroot -ppassword
```
