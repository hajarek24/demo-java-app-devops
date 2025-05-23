FROM openjdk:17-jdk-slim

WORKDIR /opt/app

COPY target/demo-java-app.jar app.jar

ENTRYPOINT ["java", "-jar", "app.jar"]