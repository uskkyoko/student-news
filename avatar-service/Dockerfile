#starts the build stage
FROM maven:3.10-eclipse-temurin-27 AS build
WORKDIR /app

# Cache dependencies separately so source changes can reuse this layer.
COPY pom.xml .
# downloads maven dependencies
RUN mvn -B -q dependency:go-offline 
#copies source code 
COPY src ./src
#builds the application jar
RUN mvn -B -q clean package -DskipTests

#start of the final runtime stage
FROM eclipse-temurin:27-jre-alpine
WORKDIR /app

#creates a system group with a user and dest for avatars
RUN addgroup -S -g 10001 app \
    && adduser -S -u 10001 -G app app \
    && mkdir -p /data/avatars \
    && chown app:app /data/avatars 

#copies jar from build into runtime
COPY --from=build /app/target/avatar-service.jar /app/app.jar
USER 10001:10001
EXPOSE 8090
ENTRYPOINT ["java", "-jar", "/app/app.jar"]
