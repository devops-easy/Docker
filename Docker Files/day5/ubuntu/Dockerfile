FROM ubuntu:22.04
RUN apt update
RUN apt install openjdk-11-jdk wget -y
RUN wget https://springpetclinic-devopseasy.s3.us-west-2.amazonaws.com/spring-petclinic.jar
CMD [ "java","-jar","/spring-petclinic.jar" ]