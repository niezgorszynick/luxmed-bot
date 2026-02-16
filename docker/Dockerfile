# Stage 1: Build the application
# Using a verified image from sbtscala
FROM sbtscala/scala-sbt:eclipse-temurin-jammy-11.0.17_1.8.0_2.13.10 AS builder

WORKDIR /build

# Copy the build files first to cache dependencies
COPY project/ /build/project/
COPY build.sbt /build/build.sbt
COPY common/ /build/common/
COPY bot/ /build/bot/

# Build the fat jar (assembly)
# Note: assembly is a plugin task usually configured in the project
RUN sbt "bot/assembly"

# Stage 2: Run the application
FROM eclipse-temurin:11-jre-focal

RUN apt-get update && apt-get install -y netcat tzdata
RUN ln -fs /usr/share/zoneinfo/Europe/Warsaw /etc/localtime && \
    dpkg-reconfigure -f noninteractive tzdata

WORKDIR /app

# Copy the built jar from the builder stage
# We use a wildcard to match the specific version generated
COPY --from=builder /build/bot/target/scala-2.13/luxmed-bot-assembly-*.jar /app/server.jar
COPY docker/run.sh /app/run.sh
RUN echo "1.0.0" > /app/version

RUN chmod +x /app/run.sh
CMD ["./run.sh"]
