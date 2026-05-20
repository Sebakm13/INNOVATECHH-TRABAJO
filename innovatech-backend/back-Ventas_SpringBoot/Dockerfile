FROM maven:3.8.5-eclipse-temurin-17 AS build
WORKDIR /app
COPY Springboot-API-REST/pom.xml .
COPY Springboot-API-REST/src ./src
RUN mvn clean package -DskipTests

FROM eclipse-temurin:17-jdk-jammy
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar
RUN useradd -m devopsuser && chown -R devopsuser:devopsuser /app
USER devopsuser
EXPOSE 8082
ENTRYPOINT ["java", "-jar", "app.jar"]
