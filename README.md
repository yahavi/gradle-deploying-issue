# Gradle deployment issue reproducer

## The issue
When the root module and a sub module have the same name, the submodule's artifact does not deploy to Artifactory.

## Requirements
* Java 8.
* Gradle 4.10.3 and above. Using the gradle wrapper also works.
* Artifactory oss with Gradle default repositories:
  * `docker run --name artifactory -d -p 8081:8081 docker.bintray.io/jfrog/artifactory-oss:latest`
  * Go to Artifactory UI: `http://localhost:8081/artifactory` with *username*:admin *password*:password.
  * Create a quick setup of default Gradle repositories from `Welcome admin -> Quick setup -> Gradle -> Create`.

## Steps to reproduce
1. Use Build info Gradle extractor 4.14.1: 
```groovy
classpath(group: 'org.jfrog.buildinfo', name: 'build-info-extractor-gradle', version: '4.14.1')
```
1. Run `./gradlew clean artifactoryPublish --console=verbose`
1. See that only `shared-1.0.jar` is published.

## The 'problematic' commit
Using the 'Searching for a Lion in the Desert' method, 
I found out that the commit that caused this issue (Without blaming anyone, of course) is: "[Improve module info extraction to gather info safely in parallel](https://github.com/jfrog/build-info/commit/76e33a50ff90492ae355b4410d01e75359c8c904)".

## Workarounds
* Changing Build info Gradle extractor version to `4.13.0`.
* Use the default groupId. The groupId will be implied by Gradle automatically from the directory.
* Change the `gradle-deploying-issue` module name to something else different from the root module.
