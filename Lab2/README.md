# Lab 2: Java Application with Gradle

## Objective
Test, build, package, and run the Calculator application using Gradle.

## Prerequisites
- OpenJDK 17
- Gradle 8.14.3

## 1. Navigate to the project

```bash
cd ~/iVolve-DevOps-labs/Lab2
```

Check the project files:

```bash
ls
```

The project includes `build.gradle`, `settings.gradle`, and `src/`.

## 2. Run the tests

Command:

```bash
gradle test
```

Output:

```text
BUILD SUCCESSFUL in 11s
3 actionable tasks: 3 executed
```

## 3. Clean and build the application

Command:

```bash
gradle clean build
```

Output:

```text
BUILD SUCCESSFUL in 5s
8 actionable tasks: 8 executed
```

This command removes previous build artifacts, compiles the application, runs tests, and builds the project.

## 4. Verify the generated JAR

Command:

```bash
ls -lh build/libs/
```

Output:

```text
total 4.0K
-rw-rw-r-- 1 user user 2.3K Oct 21 02:22 calculator.jar
```

The file size and timestamp may change on future builds.

## 5. Run the application

Command:

```bash
java -jar build/libs/calculator.jar
```

Output:

```text
Enter first number: 33
Enter second number: 02
a = 33.0, b = 2.0
Sum: 35.0
Subtract: 31.0
Multiply: 66.0
Divide: 16.5
```

## Result
- Gradle tests passed.
- The clean build completed successfully.
- The JAR was generated at `build/libs/calculator.jar`.
- The application ran successfully and returned the expected calculation results.
