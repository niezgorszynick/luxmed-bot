# Stage 1: Build the application using Gradle
FROM gradle:7.6-jdk11 AS builder

WORKDIR /build

# Copy the Gradle configuration and source code
COPY build.gradle settings.gradle ./
COPY common/ ./common/
COPY bot/ ./bot/

# Build the shadowJar (this is the Gradle equivalent of sbt assembly)
RUN gradle :bot:shadowJar --no-daemon

# Stage 2: Run the application
FROM eclipse-temurin:11-jre-focal

RUN apt-get update && apt-get install -y netcat tzdata
RUN ln -fs /usr/share/zoneinfo/Europe/Warsaw /etc/localtime && \
    dpkg-reconfigure -f noninteractive tzdata

WORKDIR /app

# Copy the built jar from the builder stage
# Gradle usually puts shadow jars in bot/build/libs/
COPY --from=builder /build/bot/build/libs/luxmed-bot-all.jar /app/server.jar
COPY docker/run.sh /app/run.sh
RUN echo "1.0.0" > /app/version

RUN chmod +x /app/run.sh
CMD ["./run.sh"]
