ARG JAR_FILE=target/*.jar

# build stage
FROM registry.access.redhat.com/ubi8/openjdk-11
COPY . .
USER root
RUN mvn clean install -DskipTests

# runtime stage
FROM registry.access.redhat.com/ubi8/openjdk-11
COPY --from=0 /home/jboss/target/*.jar /home/jboss/app.jar
ENTRYPOINT ["java","-jar","app.jar"]