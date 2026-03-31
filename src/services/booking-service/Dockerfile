FROM maven:3.8.5-openjdk-8 AS builder
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline -B
COPY src ./src
RUN mvn package -DskipTests

FROM eclipse-temurin:8-jre-alpine
RUN apk add --no-cache curl bash && \
    addgroup -S appuser && adduser -S appuser -G appuser

WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar
COPY wait-for-it.sh .
RUN chmod +x wait-for-it.sh && chown -R appuser:appuser /app

USER appuser

HEALTHCHECK --interval=30s --timeout=3s --start-period=60s --retries=3 \
  CMD curl -f http://localhost:8083/ || exit 1

EXPOSE 8083
CMD ["./wait-for-it.sh", "postgres:5432", "--", \
     "./wait-for-it.sh", "rabbitmq:5672", "--", \
     "java", "-jar", "app.jar"]