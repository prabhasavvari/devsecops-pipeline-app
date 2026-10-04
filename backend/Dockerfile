# ==========================================
# STAGE 1: Build the Application JAR
# ==========================================
FROM maven:3.9.6-eclipse-temurin-17 AS builder
WORKDIR /app

# Copy dependency configuration files first (optimizes Docker caching)
COPY pom.xml .
RUN mvn dependency:go-offline -B

# Copy the actual Java source code
COPY src ./src

# Package the application into an executable .jar file (skipping tests for speed)
RUN mvn clean package -DskipTests

# ==========================================
# STAGE 2: Lightweight Runtime Environment
# ==========================================
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app

# Security Best Practice: Create a non-root system user so the app doesn't run as root
RUN addgroup -S springgroup && adduser -S springuser -G springgroup
USER springuser

# Copy only the compiled JAR file from Stage 1 into this clean runtime container
COPY --from=builder /app/target/*.jar app.jar

# Expose the port that Spring Boot listens on
EXPOSE 8080

# Define container startup command with JVM memory limits for low-resource environments
ENTRYPOINT ["java", "-Xmx256m", "-jar", "app.jar"]