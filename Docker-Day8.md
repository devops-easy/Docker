### ENV & ARG Directive/instruction
* The ENV directive is used to set environmental variables. These environmental variables are used by applications to get information Syntax.

```
ENV <key> <value>
```
* ARG instruction is used to define the variables that the user can pass while building the image Syntax

```
ARG <variablename>=<defaultvalue>
```

```
FROM tomcat:8-jdk8
EXPOSE 8080
ARG dl="https://gameoflife-devopseasy.s3.us-west-2.amazonaws.com/gameoflife.war"
ADD  ${dl} /usr/local/tomcat/webapps/gameoflife.war
CMD [ "catalina.sh","run" ]
```
* Now build the image 

![Preview](./Images/arg1.PNG)

![Preview](./Images/arg2.PNG)

* ENV variables will be available in the containers also

```
FROM tomcat:8-jdk8
EXPOSE 8080
ENV ver=1.0
ARG dl="https://gameoflife-devopseasy.s3.us-west-2.amazonaws.com/gameoflife.war"
ADD  ${dl} /usr/local/tomcat/webapps/gameoflife.war
CMD [ "catalina.sh","run" ]

FROM tomcat:8-jdk8
EXPOSE 8080
ENV ver=1.0
ENV location="/usr/local/tomcat/webapps/gameoflife.war"
ARG dl="https://gameoflife-devopseasy.s3.us-west-2.amazonaws.com/gameoflife.war"
ADD  ${dl} ${location}
CMD [ "catalina.sh","run" ]
```
* Now build the docker image

![Preview](./Images/env1.PNG)

![Preview](./Images/env2.PNG)

## WORKDIR instruction
* By default if we donot specify the default working directory is /
* Where as in Dockerfile it is possible to change the working directory

```
FROM amazoncorretto:11-alpine-jdk
WORKDIR /etc
ADD https://springpetclinic-devopseasy.s3.us-west-2.amazonaws.com/spring-petclinic.jar /spring-petclinic.jar
EXPOSE 8080
CMD ["java","-jar","/spring-petclinic.jar"]
```
* Now build the docker image

![Preview](./Images/wrk1.PNG)

![Preview](./Images/wrk2.PNG)

## ADD and COPY instruction
* During the docker image build process we might require to copy files from our local system into docker image filesystem. This can be acheived by using COPY/ADD instruction

```
COPY <source> <destination>
ADD <source> <destination>
```
* In some cases while building docker image you might require to download the files from some urls and copy to the docker file system, then we can use ADD instruction

```
ADD <source> <destination>
```
* Create a sample index.html file for practical implementation

```
<html>
    <body>
        <h1>Welcome to Devops Easy</h1>
    </body>
</html> 
```

* Now create a Dockerfile in same location of index.html file

```
FROM ubuntu:18.04
LABEL author="DevopsEasy"
RUN apt-get update && apt-get install apache2 -y
WORKDIR /var/www/html/
COPY index.html .
CMD ["echo","helloworld"] 
```
* Try to build the docker image and check the behavior of COPY and workdir

## USER, HEALTHCHECK and EXPOSE instructions
* Docker will use the root as the default user in the docker containers
* USER instruction can change this behavior and specify a non -root user ad default user

```
USER <user>
USER <user>:group
```
* Lets create a Dockerfile based on Apache Server

```
FROM ubuntu:18.04
LABEL author="Josef Jackson"
LABEL organization="DevopsEasy"
RUN apt update && apt-get install apache2 -y
USER www-data
CMD ["whoami"]
```
* Now build the docker image

```
docker image build -t userdemo:1.0 .
```
![Preview](./Images/docker-user1.png)

* Create a container and keep it running in the background

![Preview](./Images/docker-user2.png)

![Preview](./Images/docker-user3.png)

* EXPOSE instruction is used to inform Docker that a container is listenting on the specified port at run time

```
EXPOSE <port>
EXPOSE <port>/<protocol>
```
## Accessing the applications inside docker containers
* From now the machine where we have installed docker will referred as host and the docker container will be referred as container
* We have access to host network & as of now containers are created in private container network, so to access applications inside containers we use port-forwording
* Ports exposed Expose instruction will only be accesible within docker container
* To access the ports from the host we can use ``` -p <host-port>:<container-port> or -P ```

![Preview](./Images/docker-port.png)

* command: ``` docker container run -d -p <host-port>:<container-port> <image> ```
* Create a nginx container and expose on port 30000 ``` docker container run -d -p 30000:80 --name nginx1 nginx ```

* Create a jenkins container & expose 8080 port on 30001 port of host ``` docker container run -d -p 30001:8080 --name jenkins1 jenkins/jenkins ```
* To assing any random free port on host to container port ``` docker container run -d -P image ```
* Lets create 3 nginx containers

```
docker container run -d --name ng1 -P nginx
docker container run -d --name ng2 -P nginx
docker container run -d --name ng3 -P nginx
```

```
docker container run -d -p 8080:80 --name apache1 httpd
docker container run -d -P --name apache1 httpd
```

* To verify if the application is running or not we can create HEALTHCHECK insturction

```
HEALTHCHECK --internal=1m --timeout=2s --retries=3 CMD curl -f http://localhost/ || exit 1
```
```
FROM ubuntu:18.04
LABEL author="Josef Jackson"
LABEL organization="DevopsEasy"
RUN apt update && apt-get install apache2 -y && apt install curl -y
HEALTHCHECK CMD curl -f http://localhost/ || exit 1
EXPOSE 80
CMD ["apache2ctl", "-D", "FOREGROUND"]
```
![Preview](./Images/docker-healthcheck.png)
## ENTRYPOINT & CMD
* Create a Docker container with any base image of your choice which will print  ``` Hello Docker ``` when the container is STARTED

```
FROM alpine
CMD ["echo", "Hello Docker"]
```
* Lets do some experiments

```
docker container run cmd:1.0 

docker container run cmd:1.0 pwd
```
* Whatever is written in CMD can be overriten by passing arguments after image name in docker container run ``` docker container run cmd:1.0 <any command> ```
* Now consider the following dockerfile

```
FROM alpine
ENTRYPOINT ["echo"]
CMD ["Hello Docker"]
```
* Now build the image 

```
docker image build -t cmd-ent:1 .
```
* Now run the container

```
docker container run cmd-ent:1 

docker container run cmd-ent:1 pwd

docker container run cmd-ent:1 Docker

## test
echo pwd
echo Docker
```
* ENTRYPOINT is the fixed command that gets started when container is created and cannot be overriten whereas whatever is written in CMD can be overriten

## CMD
* This instruction is executed when the container is created and can be overritten by passing arguments when creating container 

## ENTRYPOINT
* This instruction is executed when the container is created and cannot be overritten by passing arguments when creating container
* Generally in entrypoint we give executable path and in CMD we give arguments for the containers where the command to be executed when starting containers is fixed.

```
java -jar spc.jar


Give flexibility to run anything during creating container

CMD ["java", "-jar", "spc.jar"]


Dont give flexibility
ENTRYPOINT ["java", "-jar", "spc.jar"]

Give option on arguments to be passed
ENTRYPOINT ["java"]
CMD ["-jar", "spc.jar"]
```
### ENTRYPOINT and CMD Example
1. Create a image with tag testing:1.0 with the following Dockerfile

```
FROM amazoncorretto:11-alpine-jdk
ADD https://springpetclinic-devopseasy.s3.us-west-2.amazonaws.com/spring-petclinic.jar /spring-petclinic.jar
EXPOSE 8080
ENTRYPOINT ["java","-jar","/spring-petclinic.jar"]
```
2. Create a image with tag testing:2.0 with the following Dockerfile
```
FROM amazoncorretto:11-alpine-jdk
ADD https://springpetclinic-devopseasy.s3.us-west-2.amazonaws.com/spring-petclinic.jar /spring-petclinic.jar
EXPOSE 8080
CMD ["java","-jar","/spring-petclinic.jar"]
```
* Create a image with tag testing:3.0 with the following Dockerfile
```
FROM amazoncorretto:11-alpine-jdk
ADD https://springpetclinic-devopseasy.s3.us-west-2.amazonaws.com/spring-petclinic.jar /spring-petclinic.jar
EXPOSE 8080
ENTRYPOINT ["java"]
CMD ["-jar",  "/spring-petclinic.jar"]
```
4. Execute docker image ls 
5. Now execute the following commands

```
docker container run -P -d testing:1.0
docker container run -P -d testing:2.0
docker container run -P -d testing:3.0
docker container ls
```
6. To the container run command we can pass arguments 

![Preview](./Images/cmd-entrypoint.png)

7. ENTRYPOINT cannot be changed with args but whatever we write in CMD can be overwritten with args 


![Preview](./Images/cmd-entrypoint1.png)

```
# no impact on application startup
docker container run -P testing:1.0 echo helloworld

# startup is changed
docker container run -P testing:2.0 echo helloworld

# startup is notchanged but the arguments are changed
docker container run -P testing:3.0 echo helloworld
```

![Preview](./Images/cmd-entrypoint2.png)


![Preview](./Images/cmd-entrypoint3.png)


![Preview](./Images/cmd-entrypoint4.png)