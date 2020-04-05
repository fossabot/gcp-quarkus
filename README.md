<img src="https://github.com/didier-durand/CloudRun4Java/blob/master/img/quarkus-logo.svg" height="100">     <img src="https://github.com/didier-durand/CloudRun4Java/blob/master/img/google-cloud-run-logo.png" height="100">   <img src="https://github.com/didier-durand/CloudRun4Java/blob/master/img/docker-logo.png" height="100"><img src="https://github.com/didier-durand/CloudRun4Java/blob/master/img/java-logo.png" height="100">

# gcp-quarkus

![Java CI with Maven](https://github.com/didier-durand/gcp-quarkus/workflows/Java%20CI%20with%20Maven/badge.svg)

Sample Quarkus Java application adapted to become executable as microservice packaged in Docker container on Google Cloud Run.

This **__requires__** Java version 11 to work. Our trials with former versions (v8) were unsuccessful due to failure in second stage of container build. Also, Google Cloud Platform SDK must be installed (version 287.0.0 as of writing). GitHub workflow [yaml file](https://github.com/didier-durand/gcp-quarkus/blob/master/.github/workflows/maven.yml) has to abe upgraded to Java 11 as well.

This project containing the Quarkus sample application was initially generated via Maven CLI as recommended by official tutorial ["Getting stated"](https://quarkus.io/guides/getting-started) on [quarkus.io](https://quarkus.io/) web site 

```
mvn io.quarkus:quarkus-maven-plugin:1.3.1.Final:create \
    -DprojectGroupId=org.acme \
    -DprojectArtifactId=getting-started \
    -DclassName="org.acme.getting.started.GreetingResource" \
    -Dpath="/hello"
```
    
## Changes after project generation

1. The .dockerignore file must be deleted as it hinders important artefacts to be embarked in Google Cloud remote build.
2. A multi-stage Dockerfile is added to root directory ([copy of src/main/docker/Dockerfile.jvm](https://github.com/didier-durand/gcp-quarkus/blob/master/src/main/docker/Dockerfile.jvm)).
3  First build stage is added to run Maven on Google Cloud Build. Note: "as builder" added to be able to reference artefacts produced first stage duing second one. 
4. Second stage (coming from initial sample) must copy files from stage1 WORKDIR, hence "--from=builder" and "app/" in the COPY commands.

## Execution on Google Cloud Platform

bash ./sh/gcloud-build-container.sh gcp-gae-gwt-template gcp-quarkus