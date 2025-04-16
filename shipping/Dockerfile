#
# Build Stage
#

FROM debian:10 AS build

# Install Java, curl, tar
RUN apt-get update && \
    apt-get install -y curl tar openjdk-11-jdk

# Set Maven version
ENV MAVEN_VERSION=3.8.8

# Install Maven
RUN curl -fsSL https://downloads.apache.org/maven/maven-3/${MAVEN_VERSION}/binaries/apache-maven-${MAVEN_VERSION}-bin.tar.gz -o /tmp/maven.tar.gz && \
    mkdir -p /opt/maven && \
    tar -xzf /tmp/maven.tar.gz -C /opt/maven --strip-components=1 && \
    rm /tmp/maven.tar.gz

# Add Maven to PATH
ENV MAVEN_HOME=/opt/maven
ENV PATH=$MAVEN_HOME/bin:$PATH

# Set working directory
WORKDIR /opt/shipping

# Copy project files and build
COPY pom.xml .
RUN mvn dependency:resolve

COPY src ./src
RUN mvn package -DskipTests

#
# Runtime Stage
#
FROM openjdk:8-jdk

# Set working directory
WORKDIR /opt/shipping

# App configs
EXPOSE 8080
ENV CART_ENDPOINT=cart:8080
ENV DB_HOST=mysql

# Copy built JAR from build stage
COPY --from=build /opt/shipping/target/shipping-1.0.jar shipping.jar


# Run the app
CMD ["java", "-Xmn256m", "-Xmx768m", "-jar", "shipping.jar"]
