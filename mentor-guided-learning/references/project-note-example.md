# Project Note 01: Initialize a Spring Boot Project

**Date:** 2026-01-27
**Goal:** Create a runnable Spring Boot service from scratch, and verify it starts locally.

---

## Why This Matters (Intuition First)

If your project is a house, project initialization is pouring the foundation.

- If the foundation is messy (wrong Java version, missing Gradle wrapper, unclear folder layout), everything you build later will feel harder than it needs to be.
- If the foundation is clean (consistent toolchain, reproducible build, predictable structure), you move faster and debug with less pain.

So in this note, we are not trying to build business features yet. We are only trying to get a boring but reliable baseline: "I can run the app. I know what files matter. I can verify success." That baseline is surprisingly valuable.

---

## Prerequisites

- JDK: 21
- Spring Boot: 3.x
- Build tool: Gradle
- Audience: Java/Spring Boot beginners

---

## Reading Guide

- Must read (10-15 min): Why This Matters -> Project Creation -> Start -> Validate
- Optional (as needed): Project Structure -> Common Issues -> Glossary

---

## Background

### What is Spring Boot?

**Spring Boot** is a Java framework for building web apps and REST APIs with less setup.

Plain explanation:
- Similar role to Express in Node.js or FastAPI in Python: it helps you expose HTTP endpoints.
- It hides a lot of "plumbing" (configuration, dependency wiring) so you can focus on your application logic.

A useful mental model:
- Java + Spring Boot is like "a well-organized workshop". You can still build anything you want, but the tools are already placed where you expect them.

### Core Concepts (Quick Table)

| Concept | What it is | Analogy |
| --- | --- | --- |
| Spring Boot | Web framework | Express (Node.js) / FastAPI (Python) |
| Gradle | Build tool | npm / pip |
| Maven | Alternative build tool | Similar to Gradle, but XML-based |
| JPA | Database access abstraction | ORM like SQLAlchemy / Sequelize |

---

## Project Creation

We want the fastest path to a consistent, reproducible project. There are two good ways.

### Method A: Spring Initializr (Web)

This is the quickest way to generate a correct project skeleton.

Steps:
1. Open Spring Initializr (search "Spring Initializr" or use the official site).
2. Choose:
   - Project: Gradle - Groovy
   - Language: Java
   - Spring Boot: 3.x (or latest 3.x)
   - Java: 21
3. Fill project metadata:
   - Group: `com.example`
   - Artifact: `platform-api`
   - Name: `platform-api`
4. Add dependencies (minimal starter set):
   - Spring Web
   - (Optional) Spring Data JPA
   - (Optional) MySQL Driver
   - (Optional) Lombok
5. Generate the ZIP and extract it into your project directory.

Notes:
- Start minimal. You can always add dependencies later.
- If you are not sure about DB yet, skip JPA and MySQL for now.

### Method B: IntelliJ IDEA (Recommended If You Use IntelliJ)

IntelliJ can create the same project but with IDE integration.

Steps:
1. File -> New -> Project
2. Choose Spring Initializr
3. Use the same config as Method A
4. Select dependencies -> Finish

---

## Project Structure Overview

Generated structure (typical):

```text
platform-api/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/example/platformapi/
│   │   │       └── PlatformApiApplication.java  <- app entry
│   │   └── resources/
│   │       └── application.yml                  <- config
│   └── test/                                    <- tests
├── build.gradle                                 <- dependencies
└── gradlew                                      <- Gradle wrapper
```

If you only remember three files:
- `gradlew`: ensures builds work even when Gradle is not installed globally.
- `build.gradle`: your dependencies and build configuration.
- `PlatformApiApplication.java`: the entry point.

---

## Key Files and Code

### `src/main/java/.../PlatformApiApplication.java`

This is the application entry point.

```java
@SpringBootApplication
public class PlatformApiApplication {
    public static void main(String[] args) {
        SpringApplication.run(PlatformApiApplication.class, args);
    }
}
```

Explanation:
- `@SpringBootApplication` enables common Spring Boot auto-configuration.
- `SpringApplication.run(...)` starts the app and the embedded server.

### `build.gradle`

Dependencies live here.

```gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    // implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    // runtimeOnly 'com.mysql:mysql-connector-j'
}
```

Explanation:
- Start with `spring-boot-starter-web` for REST APIs.
- Uncomment JPA/MySQL later when you actually need persistence.

### `src/main/resources/application.yml`

Configuration lives here.

```yaml
server:
  port: 8080
```

---

## Start the Project

We want a first successful startup as early as possible.

### Option 1: Run in IntelliJ

1. Locate `PlatformApiApplication.java`
2. Right click -> Run
3. Look for a log line like:

```text
Started PlatformApiApplication in ... seconds
```

### Option 2: Run in terminal

```bash
cd platform-api
./gradlew bootRun
```

Command interpretation:
- `./gradlew`: uses the project-local Gradle wrapper (reproducible builds)
- `bootRun`: runs the Spring Boot app

Risk note:
- Low risk: does not modify your business code
- May download dependencies
- Binds to local port 8080 by default

---

## Validate Success

We are not validating features yet. We only validate "the server is up".

### Method 1: Browser

Visit:

```text
http://localhost:8080
```

Expected outcomes:
- 404 or a default error page can still mean success: the server is running but no route is defined.
- "Site can't be reached" means the app likely did not start.

### Method 2: curl

```bash
curl -i http://localhost:8080
```

What you want to see:
- A HTTP status line (even 404 is fine)

---

## Common Issues

### Q1: Port already in use

Error:

```text
Port 8080 was already in use
```

Cause:
- Another process is using port 8080.

Fix A: change port

```yaml
server:
  port: 8081
```

Fix B: find and stop the process

```bash
lsof -i :8080
kill -9 <PID>
```

### Q2: Java/Gradle not found

Error examples:
- `JAVA_HOME not set`
- `java: command not found`

Fix:
- Install JDK 21
- Ensure your IDE or shell is configured to use it

### Q3: Dependency download is slow

Cause:
- Maven Central network latency.

Fix:
- Configure a faster mirror in Gradle repositories if needed.

---

## Next Steps

Once startup is stable, you can safely begin real work:

1. Add a health endpoint (`GET /health`) so validation is explicit.
2. Add structured logging.
3. Add one vertical slice feature (one endpoint + one service + one test).

---

## Three-Line Summary

1. Linear regression is not the goal here; a clean buildable project is the goal.
2. Spring Initializr (Web or IntelliJ) gives a correct baseline quickly.
3. If `localhost` responds (even 404), your app is likely running successfully.

## Review Checklist

- [ ] I can explain what Spring Boot is in one sentence.
- [ ] I can find the entry point class and run it.
- [ ] I can validate startup from browser or curl.
