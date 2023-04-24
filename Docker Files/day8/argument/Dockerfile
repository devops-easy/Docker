FROM tomcat:8-jdk8
EXPOSE 8080
ARG dl="https://gameoflife-devopseasy.s3.us-west-2.amazonaws.com/gameoflife.war"
ADD  ${dl} /usr/local/tomcat/webapps/gameoflife.war
CMD [ "catalina.sh","run" ]

