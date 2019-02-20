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
Catalog Service  | 8800 ~  | Java
Payment Service  | 8900 ~  | Java
Product Service  | 9000 ~  | Kotlin
FrontEnd Service  | 14000 ~  | Java

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

![](https://s3-eu-west-1.amazonaws.com/videotestpoc/Screen+Shot+2019-02-04+at+13.50.15.png)

10. To use turbine through docker give below link as streamer

```
 turbine:9999/turbine.stream
```

![]()
[![](https://s3-eu-west-1.amazonaws.com/videotestpoc/hystrix-turbine.gif)](https://s3-eu-west-1.amazonaws.com/videotestpoc/turbine.mov)
