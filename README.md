# ☸️ DO10_BasicK8s – Основы Kubernetes

**Проект по развёртыванию микросервисного приложения в Kubernetes с использованием манифестов, ConfigMap, Secret и стратегий обновления**

[![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?logo=kubernetes&logoColor=white)](https://kubernetes.io/)
[![Minikube](https://img.shields.io/badge/Minikube-2C5F2D?logo=kubernetes&logoColor=white)](https://minikube.sigs.k8s.io/)
[![Docker](https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white)](https://www.docker.com/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![RabbitMQ](https://img.shields.io/badge/RabbitMQ-FF6600?logo=rabbitmq&logoColor=white)](https://www.rabbitmq.com/)
[![Java](https://img.shields.io/badge/Java-ED8B00?logo=java&logoColor=white)](https://www.java.com/)

## 📋 О проекте

Проект по знакомству с основами Kubernetes на примере микросервисного приложения бронирования отелей. Включает:
- Запуск локального кластера Minikube
- Применение готового манифеста (тестовое приложение)
- Написание собственных манифестов для 7 микросервисов, PostgreSQL и RabbitMQ
- Использование ConfigMap и Secret для управления конфигурацией
- Сравнение стратегий развертывания (Recreate vs RollingUpdate)

## 📚 Документация

- [**Описание задания**](docs/README_RUS.md) – детальное описание задач
- [**Отчёт о выполнении**](src/report.md) – подробный отчёт с описанием всех этапов и скриншотами

## 🏗️ Структура проекта

```
DO10_BasicK8s/
├── README.md
├── docs/
│ └── README_RUS.md
├── src/
│ ├── report.md
│ ├── screen/ # Скриншоты выполнения
│ ├── example/ # Готовый манифест
│ │ └── microservices.yml
│ ├── k8s/ # Собственные манифесты
│ │ ├── configmap.yaml # ConfigMap с настройками
│ │ ├── secret.yaml # Secret с паролями и ключами
│ │ ├── postgres-deploy.yaml # PostgreSQL
│ │ ├── rabbitmq-deploy.yaml # RabbitMQ
│ │ ├── booking-deploy.yaml # booking-service
│ │ ├── gateway-deploy.yaml # gateway-service
│ │ ├── hotel-deploy.yaml # hotel-service
│ │ ├── loyalty-deploy.yaml # loyalty-service
│ │ ├── payment-deploy.yaml # payment-service
│ │ ├── report-deploy.yaml # report-service
│ │ ├── session-deploy.yaml # session-service
│ │ └── ... варианты для стратегий
│ ├── services/ # Dockerfile для микросервисов
│ │ ├── booking-service/
│ │ │ ├── Dockerfile
│ │ │ └── wait-for-it.sh
│ │ ├── gateway-service/
│ │ ├── ...
│ │ └── database/
│ │ ├── Dockerfile
│ │ └── init.sql
│ └── application_tests.postman_collection.json

```

## 🛠️ Выполненные задачи

### ✅ **Part 1. Использование готового манифеста**
- Запуск Minikube с драйвером `vfkit` (для macOS)
- Применение манифеста `microservices.yml` (4 приложения)
- Проверка работы через `minikube service apache`
- Запуск Kubernetes Dashboard

### ✅ **Part 2. Написание собственного манифеста**
- Создание **ConfigMap** `app-config` для хранения несекретных параметров (адреса, порты)
- Создание **Secret** `app-secret` с паролями БД и RabbitMQ, а также RSA-ключами
- Написание манифестов для PostgreSQL и RabbitMQ (сборка образов внутри Minikube)
- Написание манифестов для 7 микросервисов (Deployment + Service)
- Проверка статуса объектов (`kubectl get/describe`)
- Декодирование секретов для проверки
- Прокидывание портов через `kubectl port-forward`
- Запуск Postman-тестов и проверка работы приложения
- Сравнение стратегий обновления **Recreate** и **RollingUpdate** (замер времени)

## 💡 Приобретённые навыки

- **Запуск локального Kubernetes** с Minikube
- **Написание манифестов** (Deployment, Service, ConfigMap, Secret)
- **Управление конфигурацией** через ConfigMap и Secret
- **Работа с образами** внутри Minikube (`eval $(minikube docker-env)`)
- **Диагностика** объектов (`kubectl describe`, `kubectl logs`)
- **Прокидывание портов** для доступа к сервисам
- **Стратегии развертывания** (Recreate vs RollingUpdate)

## 🚀 Как использовать этот проект

### Предварительные требования
- Установленный **Minikube** (рекомендуется драйвер `vfkit` для macOS)
- **kubectl** (управление кластером)
- **Docker** (для сборки образов)

### Запуск
```bash
# Клонируем репозиторий
git clone https://github.com/Swarthma/DO10_BasicK8s.git
cd DO10_BasicK8s

# Запускаем Minikube (4 ГБ памяти)
minikube start --memory=4096 --driver=vfkit

# Применяем готовый манифест (тестовое приложение)
kubectl apply -f src/example/microservices.yml

# Проверяем работу Apache
minikube service apache

# Создаём ConfigMap и Secret
kubectl apply -f src/k8s/configmap.yaml
kubectl apply -f src/k8s/secret.yaml

# Собираем образы микросервисов внутри Minikube
eval $(minikube docker-env)
cd src/services/database && docker build -t custom-postgres:13 .
cd ../booking-service && docker build -t booking-service:latest .
# ... аналогично для остальных сервисов

# Применяем манифесты
kubectl apply -f src/k8s/postgres-deploy.yaml
kubectl apply -f src/k8s/rabbitmq-deploy.yaml
kubectl apply -f src/k8s/session-deploy.yaml
kubectl apply -f src/k8s/hotel-deploy.yaml
kubectl apply -f src/k8s/booking-deploy.yaml
kubectl apply -f src/k8s/payment-deploy.yaml
kubectl apply -f src/k8s/loyalty-deploy.yaml
kubectl apply -f src/k8s/report-deploy.yaml
kubectl apply -f src/k8s/gateway-deploy.yaml

# Прокидываем порты для тестирования
kubectl port-forward service/gateway-service 8087:8087 &
kubectl port-forward service/session-service 8081:8081 &

# Запускаем тесты Postman (Newman)
newman run src/application_tests.postman_collection.json
```

## 📊 Результаты проекта

- **Полностью рабочее микросервисное приложение** в Kubernetes
- **ConfigMap и Secret** с конфигурацией
- **Сравнение стратегий обновления**: RollingUpdate быстрее (0.291 с) и без простоя против Recreate (1.611 с)
- **Успешное прохождение Postman-тестов**
- **Навыки управления кластером** через kubectl и Dashboard

## 🔗 Связанные проекты

- [**D07 – Docker Compose & Swarm**](https://github.com/Swarthma/D07_Docker_Compose) – основа микросервисов
- [**D09 – Monitoring**](https://github.com/Swarthma/DO9_Monitoring) – observability
- [**D08 – Ansible & Consul**](https://github.com/Swarthma/D08_AutomationTools) – автоматизация
- [**D06 – CI/CD с GitLab**](https://github.com/Swarthma/D06_CICD) – непрерывная интеграция

---

**Проект выполнен в образовательных целях для освоения основ Kubernetes и оркестрации контейнеров.**

## 👨‍💻 Автор

**Галиуллин Линар** – начинающий DevOps инженер

[![Email](https://img.shields.io/badge/Email-linar.9207@gmail.com-D14836?logo=gmail)](mailto:linar.9207@gmail.com)
[![Telegram](https://img.shields.io/badge/Telegram-@Linar9207-2CA5E0?logo=telegram&logoColor=white)](https://t.me/Linar9207)
[![GitHub](https://img.shields.io/badge/GitHub-Swarthma-181717?logo=github)](https://github.com/Swarthma)

---

**Технологии:** Kubernetes, Minikube, Docker, PostgreSQL, RabbitMQ, Java, YAML, Postman
