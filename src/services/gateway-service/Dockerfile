FROM maven:3.8.5-openjdk-8

# Создаем пользователя
RUN groupadd -r appuser && useradd -r -g appuser appuser && \
    apt-get update && apt-get install -y netcat-openbsd && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Копируем ВСЕ файлы
COPY . .

# Собираем приложение
RUN mvn package -DskipTests

# Меняем владельца
RUN chown -R appuser:appuser /app

USER appuser

HEALTHCHECK --interval=30s --timeout=3s --start-period=60s --retries=3 \
  CMD nc -z localhost 8087 || exit 1


EXPOSE 8087

CMD ["java", "-jar", "target/gateway-service-0.0.1-SNAPSHOT.jar"]