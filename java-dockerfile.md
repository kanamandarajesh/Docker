
```
# Step 1: Use the official OpenJDK image from Docker Hub
FROM openjdk:11-jre-slim

# Step 2: Set the working directory inside the container
WORKDIR /app

# Step 3: Copy the compiled Java JAR file into the container
COPY target/my-app.jar /app/my-app.jar

# Step 4: Expose the port your app will run on (default is 8080)
EXPOSE 8080

# Step 5: Define the command to run the Java application
CMD ["java", "-jar", "my-app.jar"]
```


```
# ----------- Stage 1: Build the Java application -----------
FROM maven:3.9.6-eclipse-temurin-17 AS build

# Set the working directory
WORKDIR /build

# Copy the project files into the container
COPY pom.xml .
COPY src ./src

# Package the application (you can use -DskipTests if needed)
RUN mvn clean package -DskipTests

# ----------- Stage 2: Create a lightweight runtime image -----------
FROM openjdk:17-jre-slim

# Set the working directory in the runtime container
WORKDIR /app

# Copy the built JAR from the build stage
COPY --from=build /build/target/*.jar /app/app.jar

# Expose the port (default for Spring Boot or many Java web apps)
EXPOSE 8080

# Run the application
CMD ["java", "-jar", "app.jar"]

```
