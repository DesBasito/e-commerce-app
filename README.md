# E-commerce Микросервисное Приложение

## 📋 Описание проекта

Это полнофункциональное микросервисное приложение для электронной коммерции, построенное с использованием Spring Boot и Spring Cloud. Архитектура включает в себя несколько взаимодействующих сервисов, обеспечивающих полный цикл обработки заказов от создания клиента до отправки уведомлений.

## 🏗️ Архитектура системы

### Микросервисы

1. **Config Server** (порт 8888) - Централизованное управление конфигурациями
2. **Discovery Service** (порт 8761) - Eureka сервер для service discovery
3. **Gateway Service** - API Gateway для маршрутизации запросов
4. **Customer Service** (порт 8090) - Управление клиентами
5. **Product Service** (порт 8050) - Управление товарами
6. **Order Service** - Обработка заказов
7. **Payment Service** - Обработка платежей
8. **Notification Service** - Система уведомлений

### Базы данных и инфраструктура

- **PostgreSQL** (порт 5555) - Основная реляционная БД
- **MongoDB** (порт 27017) - NoSQL база для клиентов и уведомлений
- **Apache Kafka** - Асинхронная обработка сообщений
- **pgAdmin** (порт 5050) - Веб-интерфейс для PostgreSQL
- **Mongo Express** (порт 8081) - Веб-интерфейс для MongoDB
- **MailDev** (порты 1080/1025) - Тестирование email уведомлений

## 🛠️ Технологический стек

- **Java 17**
- **Spring Boot 3.5.3**
- **Spring Cloud 2025.0.0**
- **Maven 3.9.10+**
- **PostgreSQL** - для Orders, Products, Payments
- **MongoDB** - для Customers и Notifications
- **Apache Kafka** - для асинхронного обмена сообщениями
- **Docker & Docker Compose** - для контейнеризации
- **Eureka** - Service Discovery
- **Spring Cloud Gateway** - API Gateway
- **Spring Cloud Config** - Централизованная конфигурация
- **OpenFeign** - Межсервисное взаимодействие
- **Thymeleaf** - Email шаблоны
- **Lombok** - Уменьшение boilerplate кода
- **Flyway** - Миграции БД

## 🚀 Быстрый старт

### Предварительные требования

- Java 17 или выше
- Maven 3.6+
- Docker и Docker Compose
- Git

### Установка и запуск

1. **Клонирование репозитория**
```bash
git clone <repository-url>
cd e-commerce-app
```

2. **Инициализация субмодулей (если используются)**
```bash
git submodule update --init --recursive
```

3. **Запуск инфраструктуры**
```bash
docker-compose up -d
```

4. **Сборка всех сервисов**
```bash
# Сборка config-server
cd services/config-server
./mvnw clean install
cd ../..

# Сборка discovery
cd services/discovery
./mvnw clean install
cd ../..

# Аналогично для остальных сервисов
```

5. **Запуск сервисов в правильном порядке**

**Шаг 1:** Запустите Config Server
```bash
cd services/config-server
./mvnw spring-boot:run
```

**Шаг 2:** Запустите Discovery Service
```bash
cd services/discovery
./mvnw spring-boot:run
```

**Шаг 3:** Запустите остальные сервисы
```bash
# В отдельных терминалах
cd services/customer && ./mvnw spring-boot:run
cd services/product && ./mvnw spring-boot:run
cd services/order && ./mvnw spring-boot:run
cd services/payment && ./mvnw spring-boot:run
cd services/notification && ./mvnw spring-boot:run
cd services/gateway && ./mvnw spring-boot:run
```

## 📊 Мониторинг и управление

### Веб-интерфейсы

- **Eureka Dashboard**: http://localhost:8761
- **pgAdmin**: http://localhost:5050
    - Email: pgadmin@pgadmin.org
    - Password: admin
- **Mongo Express**: http://localhost:8081
- **MailDev**: http://localhost:1080

### Конфигурация баз данных

#### PostgreSQL
- Host: localhost:5555
- Database: postgres, product
- Username: qwe
- Password: qwe

#### MongoDB
- Host: localhost:27017
- Username: qwe
- Password: qwe
- Databases: customer

## 🔄 Потоки данных

### Процесс обработки заказа

1. **Создание клиента** → Customer Service (MongoDB)
2. **Просмотр товаров** → Product Service (PostgreSQL)
3. **Создание заказа** → Order Service:
    - Проверка клиента через Customer Service
    - Резервирование товаров через Product Service
    - Создание записи заказа (PostgreSQL)
4. **Обработка платежа** → Payment Service (PostgreSQL)
5. **Уведомления** → Notification Service:
    - Kafka сообщения от Order и Payment сервисов
    - Email уведомления через MailDev
    - Сохранение в MongoDB

## 📡 API Endpoints

### Customer Service (порт 8090)
```
POST   /api/v1/customer              - Создание клиента
PUT    /api/v1/customer              - Обновление клиента
GET    /api/v1/customer              - Получение всех клиентов
GET    /api/v1/customer/{id}         - Получение клиента по ID
GET    /api/v1/customer/exists/{id}  - Проверка существования клиента
DELETE /api/v1/customer/{id}         - Удаление клиента
```

### Product Service (порт 8050)
```
GET    /api/v1/products              - Получение всех товаров
POST   /api/v1/products/purchase     - Покупка товаров
```

### Order Service
```
POST   /api/v1/orders                - Создание заказа
GET    /api/v1/orders                - Получение всех заказов
GET    /api/v1/orders/{id}           - Получение заказа по ID
```

### Payment Service
```
POST   /api/v1/payments              - Создание платежа
```

## ⚙️ Конфигурация

### Файлы конфигурации (Config Server)

- `application.yml` - Общие настройки Eureka
- `customer-service.yml` - Настройки MongoDB для Customer Service
- `product-service.yml` - Настройки PostgreSQL для Product Service
- `discovery-service.yml` - Настройки Eureka Server

### Переменные окружения

Основные настройки можно переопределить через переменные окружения:

```bash
# Database
POSTGRES_USER=qwe
POSTGRES_PASSWORD=qwe
POSTGRES_DB=postgres

# MongoDB
MONGO_INITDB_ROOT_USERNAME=qwe
MONGO_INITDB_ROOT_PASSWORD=qwe

# Config Server
CONFIG_SERVER_URL=http://localhost:8888

# Eureka
EUREKA_SERVER_URL=http://localhost:8761/eureka
```

## 🔍 Тестирование

### Ручное тестирование

1. **Создание клиента**:
```bash
curl -X POST http://localhost:8090/api/v1/customer \
  -H "Content-Type: application/json" \
  -d '{
    "firstName": "Иван",
    "lastName": "Иванов",
    "email": "ivan@example.com",
    "address": {
      "street": "Улица Примерная",
      "houseNumber": "123",
      "zipCode": "12345"
    }
  }'
```

2. **Создание заказа**:
```bash
curl -X POST http://localhost:8080/api/v1/orders \
  -H "Content-Type: application/json" \
  -d '{
    "reference": "ORD-001",
    "amount": 99.99,
    "paymentMethod": "CREDIT_CARD",
    "customerId": "customer-id",
    "products": [
      {
        "productId": 1,
        "quantity": 2
      }
    ]
  }'
```

## 🐛 Устранение неполадок

### Частые проблемы

1. **Сервис не может подключиться к Config Server**
    - Убедитесь, что Config Server запущен первым
    - Проверьте URL в application.yml

2. **Ошибки подключения к базе данных**
    - Убедитесь, что Docker контейнеры запущены
    - Проверьте параметры подключения в конфигурации

3. **Сервисы не регистрируются в Eureka**
    - Убедитесь, что Discovery Service запущен
    - Проверьте настройки eureka.client в конфигурации

4. **Kafka сообщения не доставляются**
    - Убедитесь, что Kafka запущен (может потребоваться отдельная настройка)
    - Проверьте конфигурацию топиков

## 📋 TODO / Планы развития

- [ ] Добавить мониторинг (Prometheus, Grafana)
- [ ] Реализовать Circuit Breaker (Resilience4j)
- [ ] Добавить централизованное логирование (ELK Stack)
- [ ] Настроить CI/CD pipeline
- [ ] Добавить автоматические тесты
- [ ] Реализовать аутентификацию и авторизацию с помощью Keycloak
- [ ] Добавить Swagger документацию
- [ ] Оптимизировать производительность

## 🤝 Вклад в проект

1. Форкните репозиторий
2. Создайте ветку для новой функции (`git checkout -b feature/AmazingFeature`)
3. Сделайте коммит изменений (`git commit -m 'Add some AmazingFeature'`)
4. Отправьте в ветку (`git push origin feature/AmazingFeature`)
5. Откройте Pull Request

## ✨ Авторы

- **Абдулбасит Мануров** - *Разработчик* - [GitHub профиль](https://github.com/DesBasito)
