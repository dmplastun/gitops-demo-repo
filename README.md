# Микросервисное приложение на Flask + Docker Compose

Это пример простого микросервисного приложения, состоящего из двух сервисов и PostgreSQL. 
Также используется Nginx как reverse proxy.

## Архитектура

- `service-a`: Flask-приложение
- `service-b`: Flask-приложение
- `db`: PostgreSQL
- `nginx`: Reverse proxy
- `test`: Тест работы сервисов

## Как запустить

1. Клонируйте репозиторий:
   ```bash
   git clone https://github.com/yourusername/microservices-app.git 
   cd microservices-app
   ```
2. Запустите контейнеры:
   ```bash
   docker compose up -d
   ```
3. Проверьте работу:
    Service A: http://localhost/service-a 
    Service B: http://localhost/service-b 

4. Остановите:
   ```bash
   docker compose down
   ```
## Тестирование 

Запустите тесты: 
   ```bash
   docker compose run test
   ```
## Автор
   Dmitry Plastun
