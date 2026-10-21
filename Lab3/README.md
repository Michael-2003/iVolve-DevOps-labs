# Lab 3: Dockerizing a Java Calculator Application

## Overview

This lab demonstrates two ways to package the same Java Maven application into a Docker image:

- **Part 1 — Build inside Docker:** Maven compiles the application inside a Docker image that includes Maven and Java 17.
- **Part 2 — Build locally, then package:** Maven compiles the application on the host machine, and Docker packages the resulting JAR in a Java 17 runtime image.

Source application: [CalculatorMavenApp](https://github.com/Ibrahim-Adel15/CalculatorMavenApp.git)

Both approaches use a non-root user and declare port `8080` in the Dockerfile.

> **Note:** `EXPOSE 8080` documents a port; it does not make a command-line calculator an HTTP service. Port publishing is useful only if the application actually listens on that port.

---

## Part 1: Build the Application Inside Docker

### Approach

In Part 1, the Docker image contains Maven and Java 17. Docker copies the source project into the image, then runs Maven to compile and package the application.

### Dockerfile

```dockerfile
FROM maven:3.9-eclipse-temurin-17

WORKDIR /app

RUN useradd --create-home --uid 10001 appuser

COPY . .

RUN mvn -B package \
    && chown -R appuser:appuser /app

USER appuser

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "target/calculator.jar"]
```

The JAR filename in `ENTRYPOINT` must match the JAR Maven actually produces. If the generated filename differs, update the command accordingly.

### `.dockerignore`

```text
.git
.gitignore
target/
.idea/
.vscode/
*.iml
README.md
```

This excludes Git metadata, previous build output, IDE files, and documentation from the Docker build context. Maven creates the `target/` directory during the image build.

### Build and run

Run these commands from the Part 1 directory:

```bash
docker build -t app1 .
docker image ls app1
docker run -it --name container1 app1
```

Enter numbers when prompted and verify the calculator's arithmetic results.

After testing, clean up:

```bash
docker rm container1
docker rmi app1
```

Record the image size before removing the image.

### Result

The build takes place in a Maven-enabled image. This provides a self-contained build environment, but the image is larger because it includes more tooling and associated layers.

---

## Part 2: Build Locally and Package the JAR

### Approach

In Part 2, Maven runs on the host machine. Docker receives the compiled JAR and uses a Java 17 JRE image to run it. Maven and the source code are not needed in the final runtime image.

### Build the application locally

Run these commands from the Part 2 directory:

```bash
mvn clean package
ls -lh target/*.jar
```

Confirm that Maven completes successfully and check the exact generated JAR filename.

### Dockerfile

```dockerfile
FROM eclipse-temurin:17-jre

WORKDIR /app

RUN useradd --create-home --uid 10001 appuser

COPY --chown=appuser:appuser target/*.jar app.jar

USER appuser

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

The `COPY` instruction copies the locally built JAR into the image as `/app/app.jar`. This assumes there is exactly one JAR matching `target/*.jar`. If Maven creates multiple JAR files, specify the intended JAR filename explicitly.

### `.dockerignore`

```text
.git
.gitignore
.idea/
.vscode/
*.iml
README.md
```

Do **not** exclude `target/` in Part 2 because Docker needs the locally built JAR from that directory.

### Build and run

Run these commands from the Part 2 directory:

```bash
docker build -t app2 .
docker image ls app2
docker run -it --name container2 app2
```

Enter numbers when prompted and verify the calculator's results.

After testing, clean up:

```bash
docker rm container2
docker rmi app2
```

Record the image size before removing it.

### Result

The final image contains the Java runtime and packaged JAR rather than the Maven build environment. This makes it a lighter runtime image.

---

## Image Size Comparison

Docker reported the following values:

| Image | Disk usage | Content size |
|---|---:|---:|
| `app1:latest` — build inside Docker | 814 MB | 251 MB |
| `app2:latest` — build locally, runtime image | 427 MB | 110 MB |
| **Difference** | **387 MB** | **141 MB** |

### Percentage reduction

- **Disk usage reduction:** `(814 − 427) / 814 × 100` ≈ **47.5%**
- **Content size reduction:** `(251 − 110) / 251 × 100` ≈ **56.2%**

Based on these measurements, Part 2 is **387 MB smaller in disk usage** and **141 MB smaller in content size**.

Docker's *disk usage* and *content size* are different metrics. Their relationship can depend on image layers and Docker's storage accounting, so both figures are recorded separately as reported.

---

## Differences Between the Two Approaches

| Aspect | Part 1: Build inside Docker | Part 2: Build locally |
|---|---|---|
| Where compilation happens | Inside Docker | On the host machine |
| Base image | Maven + Java 17 | Java 17 JRE |
| Maven included in final image | Yes, in the build image | No |
| What is copied into the image | Project files and source code | Compiled JAR |
| Build requirements | Docker and access to build dependencies | Local Maven and a compatible JDK, plus Docker |
| Build consistency | Build tools can be standardized in the Docker image | Depends partly on host Java/Maven versions |
| Image size in this run | 814 MB disk usage; 251 MB content size | 427 MB disk usage; 110 MB content size |
| Main advantage | Self-contained, standardized build environment | Smaller runtime image |

### Advantages and trade-offs

**Part 1 — Build inside Docker**

- Provides a consistent Maven/Java build environment.
- Does not require Maven to be installed on the host.
- Is convenient for a self-contained build workflow and CI pipelines.
- Produces a larger image when the build environment remains in the final image.

**Part 2 — Build locally**

- Produces a smaller runtime image in this lab.
- Keeps Maven and source files out of the runtime image.
- Requires Maven and a compatible Java setup on the host.
- May be less reproducible if host tool versions differ.

For production, a common improvement is a **multi-stage Docker build**: compile the application in a Maven stage, then copy only the JAR into a Java runtime stage. This combines a Docker-controlled build environment with a smaller final image.

## Conclusion

Both approaches demonstrate how to containerize a Java application. In this lab, Part 2 resulted in a smaller image: disk usage decreased from **814 MB to 427 MB**, while content size decreased from **251 MB to 110 MB**. Building locally and packaging only the JAR reduces the runtime image's contents, while building inside Docker provides a self-contained build process.
