# Lab 1: Java Application with Maven

## Objective
Build, test, package, and run a Java Calculator application using Maven.

## 1. Navigate to the project

```bash
cd ~/iVolve-DevOps-labs/Lab1
```

Check the project files:

```bash
ls
```

Expected files include `pom.xml` and `src/`.

## 2. Run the tests

```bash
mvn test
```

Expected final output:

```text
[INFO] BUILD SUCCESS
```

## 3. Clean and package the application

```bash
mvn clean package
```

Expected final output:

```text
[INFO] BUILD SUCCESS
```

This command cleans previous build files, compiles the code, runs the tests, and packages the application.

## 4. Verify the generated JAR

```bash
ls -lh target/
```

If the generated file is named `calculator.jar`, verify it using:

```bash
ls -lh target/calculator.jar
```

## 5. Run the application

```bash
java -jar target/calculator.jar
```

Example output:

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
- Maven tests executed successfully.
- The application was packaged into a JAR file.
- The calculator was executed and its output verified.
