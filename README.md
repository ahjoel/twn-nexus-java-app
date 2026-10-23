# Java Maven App – Nexus Lab

This repository is a practice project for the **Nexus Repository Manager** module (Module 6) of the
[TechWorld with Nana DevOps Bootcamp](https://www.techworld-with-nana.com/devops-bootcamp).

The goal of the lab is to build a simple Java/Spring Boot application with Maven and publish the
resulting artifact (JAR) to a self-hosted Nexus repository.

## What this lab covers

- Installing and running Nexus Repository Manager on a cloud server (e.g. DigitalOcean droplet)
- Creating a dedicated Nexus user and role with permissions to deploy to a Maven repository
- Configuring a Maven project to upload artifacts to the `maven-snapshots` repository
- Building and deploying a JAR to Nexus with `mvn deploy`
- Browsing and querying uploaded artifacts through the Nexus UI and REST API

## Tech stack

| Tool / Library            | Version |
|---------------------------|---------|
| Java                      | 25      |
| Maven                     | 3.x     |
| Spring Boot (starter-web) | 4.1.0   |
| Logstash Logback Encoder  | 9.0     |
| JUnit Jupiter             | 6.1.0   |

## Project structure

```
.
├── pom.xml                                  # Maven build + Nexus distributionManagement
└── src/main
    ├── java/com/example/Application.java    # Spring Boot entry point
    └── resources/static/index.html          # Simple welcome page
```

## Prerequisites

- JDK 25
- Maven 3.x
- A running Nexus instance reachable on port `8081`
- A Nexus user allowed to deploy to the `maven-snapshots` repository

## Configuration

### 1. Nexus repository URL

The target repository is declared in `pom.xml`. Update the IP address to match your Nexus server:
My nexus-server-ip : http://164.92.192.39:8081

```xml
<distributionManagement>
    <snapshotRepository>
        <id>nexus-snapshots</id>
        <url>http://<nexus-server-ip>:8081/repository/maven-snapshots</url>
    </snapshotRepository>
</distributionManagement>
```

> The project version ends with `-SNAPSHOT`, so Maven deploys it to the snapshot repository.

### 2. Nexus credentials

Credentials must **not** be committed to the repository. Add them to your local Maven settings file
`~/.m2/settings.xml`. The `<id>` must match the one used in `pom.xml`:

```xml
<settings>
    <servers>
        <server>
            <id>nexus-snapshots</id>
            <username>your-nexus-user</username>
            <password>your-nexus-password</password>
        </server>
    </servers>
</settings>
```

## Usage

Build the application:

```bash
mvn clean package
```

Run it locally:

```bash
java -jar target/java-maven-app-1.1.0-SNAPSHOT.jar
```

Then open <http://localhost:8080>.

Deploy the artifact to Nexus:

```bash
mvn clean deploy
```

## Verifying the upload

In the Nexus UI, go to **Browse → maven-snapshots → com/example/java-maven-app**.

Or use the Nexus REST API:

```bash
# List all components in the maven-snapshots repository
curl -u <user>:<password> -X GET \
  'http://<nexus-server-ip>:8081/service/rest/v1/components?repository=maven-snapshots'
```
