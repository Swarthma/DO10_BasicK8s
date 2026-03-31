FROM maven:3.8.5-openjdk-8

# Объединяем команды в один RUN для уменьшения слоев
RUN groupadd -r appuser && useradd -r -g appuser appuser && \
    apt-get update && apt-get install -y curl && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Копируем только нужные для сборки файлы
COPY pom.xml .
COPY src ./src
COPY wait-for-it.sh .

# Собираем и меняем владельца за одну операцию
RUN mvn package -DskipTests && \
    chown -R appuser:appuser /app && \
    chmod +x wait-for-it.sh

USER appuser

EXPOSE 8081

# Healthcheck использует curl для проверки HTTP-ответа
HEALTHCHECK --interval=30s --timeout=3s --start-period=60s --retries=3 \
  CMD curl -f http://localhost:8081/ || exit 1

CMD ["./wait-for-it.sh", "postgres:5432", "--", "java", "-jar", "target/session-service-0.0.1-SNAPSHOT.jar"]