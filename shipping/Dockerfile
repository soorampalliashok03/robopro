#
# Build
#
# FROM ubuntu:22.04  # Or whatever base image you're using
FROM debian:10 AS build
RUN apt-get update && \
    apt-get install -y curl tar openjdk-11-jdk

# Install Maven
ENV MAVEN_VERSION=3.8.8
RUN curl -fsSL https://downloads.apache.org/maven/maven-3/${MAVEN_VERSION}/binaries/apache-maven-${MAVEN_VERSION}-bin.tar.gz -o /tmp/maven.tar.gz && \
    mkdir -p /opt/maven && \
    tar -xzf /tmp/maven.tar.gz -C /opt/maven --strip-components=1 && \
    rm /tmp/maven.tar.gz

# Add Maven to PATH
ENV MAVEN_HOME=/opt/maven
ENV PATH=${MAVEN_HOME}/bin:${PATH}

COPY pom.xml /opt/shipping/
RUN mvn dependency:resolve
COPY src /opt/shipping/src/
RUN mvn package

#
# Run
#
FROM openjdk:8-jdk
EXPOSE 8080
WORKDIR /opt/shipping
ENV CART_ENDPOINT=cart:8080
ENV DB_HOST=mysql
COPY --from=build /opt/shipping/target/shipping-1.0.jar shipping.jar
CMD [ "java", "-Xmn256m", "-Xmx768m", "-jar", "shipping.jar" ]

