# Learning Note 01: Initialize a Spring Boot Project

**Date:** 2026-01-27
**Goal:** Learn how to create a Spring Boot project from scratch

---

## Prerequisites

- JDK: 21
- Spring Boot: 3.x
- Build Tool: Gradle
- Audience: Java/Spring Boot beginners

## Reading Guide

- **Must read (10-15 min):** Background -> Project Creation Methods -> Start Project -> Validate Success
- **Optional (as needed):** Project Structure -> Common Issues -> Glossary -> References

---

## Background

### What is Spring Boot?

**Spring Boot** is a Java framework for rapidly building web apps and REST APIs.

**Plain explanation:**
- Similar role to Express in Node.js and FastAPI in Python
- Handles common tasks like HTTP requests, DB connection, and config management
- “Boot” means quick startup with less boilerplate setup

### Core Concepts

| Concept | Explanation | Analogy |
|------|------|------|
| **Spring Boot** | Java web framework | Express (Node.js) |
| **Gradle** | Build tool | npm (Node.js), pip (Python) |
| **Maven** | Another build tool | Similar to Gradle but XML-based |
| **JPA** | Database access framework | ORM like Sequelize, SQLAlchemy |

---

## Project Creation Methods

**Which method to pick:**
- Fastest start: **Method A (Web)**
- IDE-integrated setup: **Method B (IntelliJ, recommended)**

### Method A: Spring Initializr (Web)

**URL:** https://start.spring.io/

**Steps:**
1. Open the website
2. Choose:
   - Project: **Gradle - Groovy**
   - Language: **Java**
   - Spring Boot: **3.x** or latest
   - Java: **21**
3. Fill project metadata:
   - Group: `com.promptops`
   - Artifact: `platform-api`
   - Name: `platform-api`
4. Add dependencies:
   - Spring Web
   - Spring Data JPA
   - MySQL Driver
   - Lombok
5. Click **Generate** to download ZIP
6. Extract into your project directory

---

### Method B: IntelliJ IDEA (Recommended)

**Steps:**
1. Open IntelliJ IDEA
2. File -> New -> Project
3. Choose **Spring Initializr**
4. Use the same config as above
5. Next -> choose dependencies -> Finish

---

## Project Structure Overview

Generated structure:

```text
platform-api/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/promptops/platformapi/
│   │   │       └── PlatformApiApplication.java  <- app entry
│   │   └── resources/
│   │       └── application.yml                  <- config file
│   └── test/                                    <- test code
├── build.gradle                                 <- dependency config
└── gradlew                                      <- Gradle wrapper
```

### Key File Notes

#### 1. `PlatformApiApplication.java` - Entry Point

```java
@SpringBootApplication
public class PlatformApiApplication {
    public static void main(String[] args) {
        SpringApplication.run(PlatformApiApplication.class, args);
    }
}
```

**Explanation:**
- `@SpringBootApplication` marks the app boot class
- `main` is Java entry point
- `SpringApplication.run()` starts Spring Boot

#### 2. `build.gradle` - Dependency Management

```gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    runtimeOnly 'com.mysql:mysql-connector-j'
}
```

#### 3. `application.yml` - Config

```yaml
server:
  port: 8080
```

---

## Start the Project

### Method 1: Run in IntelliJ

1. Find `PlatformApiApplication.java`
2. Right click -> Run `PlatformApiApplication`
3. Watch console for:
   ```text
   Started PlatformApiApplication in ... seconds
   ```

### Method 2: Run in terminal

```bash
cd platform-api
./gradlew bootRun
```

**Command interpretation:**
- `./gradlew`: run project wrapper Gradle
- `bootRun`: Spring Boot run task

**Risk note:**
- Low risk (does not modify business code)
- Might download dependencies and bind local port 8080

---

## Validate Success

### Method 1: Browser

Visit:

```text
http://localhost:8080
```

**Expected:**
- 404 or Whitelabel Error Page -> **success** (app is running, no page defined)
- Not reachable -> **failure** (app likely not started)

### Method 2: Curl

```bash
curl http://localhost:8080
```

---

## Common Issues

### Q1: Port already in use

**Error:**
```text
Port 8080 was already in use
```

**Cause:** another process uses 8080

**Fix A:** change port
```yaml
server:
  port: 8081
```

**Fix B:** find and stop process
```bash
lsof -i :8080
kill -9 <PID>
```

---

### Q2: Java/Gradle not found

**Error:**
```text
JAVA_HOME not set
```

**Fix:**
1. Install JDK 21
2. Configure environment (IntelliJ often handles this)

---

### Q3: Dependency download too slow

**Cause:** remote Maven central latency

**Fix:** use local mirror in `build.gradle`

```gradle
repositories {
    maven { url 'https://maven.aliyun.com/repository/public' }
    mavenCentral()
}
```

---

## Next Steps

1. Connect database
2. Create first API endpoint (Controller)
3. Test API

---

## Three-Line Summary

1. Spring Boot is a fast Java web framework focused on quick startup.
2. You can initialize via web or IntelliJ with equivalent settings.
3. If `localhost` responds (even 404), app startup is generally successful.

## Review Checklist

- [ ] I can explain Spring Boot, Gradle, and JPA
- [ ] I can create a Spring Boot 3.x + Java 21 project independently
- [ ] I can run `./gradlew bootRun` and validate startup
- [ ] I can fix port conflicts and `JAVA_HOME` issues

---

## Glossary

| Term | English | Explanation |
|------|------|------|
| Annotation | Annotation | `@` metadata marker in code |
| Dependency | Dependency | external library required by project |
| Build | Build | compile, dependency fetch, package |
| Port | Port | network endpoint number |
| Starter | Starter | Spring Boot bundled dependency set |

---

## References

- [Spring Boot](https://spring.io/projects/spring-boot)
- [Spring Initializr](https://start.spring.io/)
- [Gradle](https://gradle.org/)
