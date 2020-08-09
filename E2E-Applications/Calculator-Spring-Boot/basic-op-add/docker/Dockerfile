FROM ppc64le/openjdk:8-jdk-alpine as build
WORKDIR /workspace/app

COPY mvnw .
COPY .mvn .mvn
COPY pom.xml .
COPY src src

RUN ./mvnw install -DskipTests
ADD ${JAR_FILE} app.jar

FROM openjdk:8-jdk-alpine
COPY --from=build /workspace/app/target/basic-op-add-1.0.0.jar /app
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
