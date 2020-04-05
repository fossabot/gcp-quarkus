
<img src="https://github.com/didier-durand/CloudRun4Java/blob/master/img/quarkus-logo.png" height="100">     <img src="https://github.com/didier-durand/CloudRun4Java/blob/master/img/google-cloud-run-logo.png" height="100">   <img src="https://github.com/didier-durand/CloudRun4Java/blob/master/img/docker-logo.png" height="100"><img src="https://github.com/didier-durand/CloudRun4Java/blob/master/img/java-logo.png" height="100">

# gcp-quarkus

![Java CI with Maven](https://github.com/didier-durand/gcp-quarkus/workflows/Java%20CI%20with%20Maven/badge.svg)

Sample Quarkus application adapted to become executable on Google Cloud Run.

This *requires* Java version 11 to work. Our trials with former versions (v8) were unsuccessful due to failure in second stage of container build.

This project was initially generated via Maven CLI as recommended by official tutorial ["Getting stated"](https://quarkus.io/guides/getting-started) on [quarkus.io](https://quarkus.io/) web site 

```
mvn io.quarkus:quarkus-maven-plugin:1.3.1.Final:create \
    -DprojectGroupId=org.acme \
    -DprojectArtifactId=getting-started \
    -DclassName="org.acme.getting.started.GreetingResource" \
    -Dpath="/hello"
```
    
*Changes after project generation:*

A trial tp downgrade Java to version 8 via pom.xml was unsucessful in subsequent Docker build command. Stayed at version 11

Created a multi-stage Dockerfile for gcp from Dockerfile.jvm. The .dockerignore file must be deleted as it hinders important artefacts to be embarked in Google Cloud remote build

Dockerfile is bound to a similar version than testing machine : hence, "FROM maven:3.6-jdk-11 as builder" to select builder image in the GCP multi-stage build.

Also, stage 2 must copy files from stage1 WORKDIR, hence "--from=builder" and "app/" in the COPY commands

COPY --from=builder app/target/lib/* /deployments/lib/
COPY --from=builder app/target/*-runner.jar /deployments/app.jar 
    
GCP SDK must be installed (version ; Google Cloud SDK 287.0.0 as of time of writing). 

The newly created Dockerfile.jvm.gcp has dependencies to Java 11 at both stages of the multi-stage build

bash ./sh/gcloud-build-container.sh gcp-gae-gwt-template gcp-quarkus

    
============

Original README

# getting-started project

This project uses Quarkus, the Supersonic Subatomic Java Framework.

If you want to learn more about Quarkus, please visit its website: https://quarkus.io/ .

## Running the application in dev mode

You can run your application in dev mode that enables live coding using:
```
./mvnw quarkus:dev
```

## Packaging and running the application

The application can be packaged using `./mvnw package`.
It produces the `getting-started-1.0-SNAPSHOT-runner.jar` file in the `/target` directory.
Be aware that it’s not an _über-jar_ as the dependencies are copied into the `target/lib` directory.

The application is now runnable using `java -jar target/getting-started-1.0-SNAPSHOT-runner.jar`.

## Creating a native executable

You can create a native executable using: `./mvnw package -Pnative`.

Or, if you don't have GraalVM installed, you can run the native executable build in a container using: `./mvnw package -Pnative -Dquarkus.native.container-build=true`.

You can then execute your native executable with: `./target/getting-started-1.0-SNAPSHOT-runner`

If you want to learn more about building native executables, please consult https://quarkus.io/guides/building-native-image-guide.
