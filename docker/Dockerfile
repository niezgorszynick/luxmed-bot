# Stage 1: Build the application using Gradle
FROM gradle:7.6-jdk11 AS builder

WORKDIR /build

# Copy the Gradle configuration files
COPY build.gradle settings.gradle gradle.properties ./
# Copy all modules
COPY common/ ./common/
COPY api/ ./api/
COPY bot/ ./bot/
COPY server/ ./server/

# Build the executable jar using the bootJar task (standard for Spring Boot projects)
RUN gradle :server:bootJar --no-daemon

# Stage 2: Run the application
FROM eclipse-temurin:11-jre-focal

RUN apt-get update && apt-get install -y netcat tzdata
RUN ln -fs /usr/share/zoneinfo/Europe/Warsaw /etc/localtime && \
    dpkg-reconfigure -f noninteractive tzdata

WORKDIR /app

# Copy the built jar from the builder stage
# Spring Boot places the jar in server/build/libs/
COPY --from=builder /build/server/build/libs/*.jar /app/server.jar
COPY docker/run.sh /app/run.sh
RUN echo "1.1.0" > /app/version

RUN chmod +x /app/run.sh
CMD ["./run.sh"]
