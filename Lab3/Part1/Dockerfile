FROM maven:3.9-eclipse-temurin-17

WORKDIR /app

RUN useradd --create-home --uid 10001 appuser

COPY . .

RUN mvn -B package \
    && chown -R appuser:appuser /app

USER appuser

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "target/calculator.jar"]
