## Хранение данных (ЛР3)

В рамках лабораторной работы настроено сохранение данных в PostgreSQL.

- **Volume:** `safronov-pgdata` (хранит данные БД вне контейнера).
- **Сеть:** `safronov-backend` (изолирует контейнеры и позволяет им общаться по именам).

### Запуск инфраструктуры

1. Создание volume и сети:
   ```bash
   docker volume create safronov-pgdata
   docker network create safronov-backend

2. Запуск базы данных:

  ```bash
  docker run -d \
    --name safronov-postgres \
    --network safronov-backend \
    -e POSTGRES_USER=user_80 \
    -e POSTGRES_PASSWORD=pgpass_80 \
    -e POSTGRES_DB=assistant_80 \
    -v safronov-pgdata:/var/lib/postgresql/data \
    postgres:15

3. Запуск приложения
  ```bash
  docker run -d \
    --name safronov-assistant \
    --network safronov-backend \
    -p 8080:8080 \
    -e APP_OWNER=safronov \
    -e APP_SECRET=secret80safronov \
    -e DATABASE_URL="postgresql://user_80:pgpass_80@safronov-postgres:5432/assistant_80" \
    -v $(pwd)/config:/app/config:ro \
    safronov-assistant:v3
