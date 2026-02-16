# Stage 1: Build the application
FROM sbtsoft/sbt:1.9.7-eclipse-temurin-jammy-11 AS builder

WORKDIR /build
COPY . .

# Build the assembly (fat jar)
RUN sbt assembly

# Stage 2: Run the application
FROM eclipse-temurin:11-jre-focal

RUN apt-get update && apt-get install -y netcat tzdata
RUN ln -fs /usr/share/zoneinfo/Europe/Warsaw /etc/localtime && \
    dpkg-reconfigure -f noninteractive tzdata

WORKDIR /app

# Copy the built jar from the builder stage
# Note: The path below assumes the standard sbt assembly output path
COPY --from=builder /build/bot/target/scala-*/luxmed-bot-assembly-*.jar /app/server.jar
COPY docker/run.sh /app/run.sh
RUN echo "1.0.0" > /app/version

RUN chmod +x /app/run.sh
CMD ["./run.sh"]
