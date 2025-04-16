#
# Build
#
FROM debian:10 AS build
RUN apt-get update && \
    apt-get install -y curl tar openjdk-11-jdk

# Set Maven version
ENV MAVEN_VERSION=3.8.8

# Download and install Maven manually
RUN curl -fsSL https://downloads.apache.org/maven/maven-3/${MAVEN_VERSION}/binaries/apache-maven-${MAVEN_VERSION}-bin.tar.gz -o maven.tar.gz && \
    tar -xzf maven.tar.gz -C /opt && \
    ln -s /opt/apache-maven-${MAVEN_VERSION}/bin/mvn /usr/bin/mvn
WORKDIR /opt/shipping
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

