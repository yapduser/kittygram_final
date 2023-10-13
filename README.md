# Kittygram

[![Main Kittygram workflow](https://github.com/yapduser/kittygram_final/actions/workflows/main.yml/badge.svg)](https://github.com/yapduser/kittygram_final/actions/workflows/main.yml)
[![Website](https://img.shields.io/website?url=https%3A%2F%2Fikitty.sytes.net%2F&label=ikitty.sytes.net&link=https%3A%2F%2Fikitty.sytes.net%2F)](https://ikitty.sytes.net/)


## Описание
- Проект Kittygram позволяет пользователям делиться своими фотографиями котиков и просматривать фотографии котиков других пользователей.
- При загрузке фото котика, пользователь должен ввести имя котика, год его рождения. При желании, может добавить ему достижения.
- Добавлять и просматривать фотографии могут только зарегистрированные и авторизованные пользователи.
- Только авторы могут изменять фотографии своих питомцев и описание.

## Технологии
- Python
- Docker
- PostgreSQL
- Django REST framework
- Gunicorn
- Nginx
- React

## Необходимо для запуска
💡[Docker](https://docs.docker.com/engine/install/)

## Запуск

Клонировать репозиторий:
```shell
git clone git clone <https or SSH URL>
```
Перейти в каталог проекта:
```shell
cd kittygram_final
```

## Локальное развертывание
В процессе развертывания приложения запускается скрипт `backend/run-up.sh`,
который выполняет миграции, собирает и копирует статику backend приложения. 
Создает суперпользователя django.

💡Данные суперпользователя необходимо предварительно задать в `.env` файле.

Создать .env файл со следующим содержанием:
```shell
# DB
POSTGRES_USER=<user>
POSTGRES_PASSWORD=<password>
POSTGRES_DB=<django>
DB_HOST=db
DB_PORT=5432

# Django settings
SECRET_KEY=<django_secrt_key>
DEBUG=False
ALLOWED_HOSTS=127.0.0.1;localhost;

# Superuser data
ADMIN_USERNAME=<usename>
ADMIN_EMAIL=<example@example.com>
ADMIN_PASSWORD=<password>
```
Развернуть приложение:
```shell
sudo docker compose -f docker-compose.dev.yml up
```
После успешного запуска, проект доступен на локальном IP `127.0.0.1:9000`.

## Развертывание на удаленном сервере

Создать docker images образы:
```shell
sudo docker build -t <username>/kittygram_backend backend/
sudo docker build -t <username>/kittygram_frontend frontend/
sudo docker build -t <username>/kittygram_gateway nginx/
```
Загрузить образы на Docker Hub:
```shell
sudo docker push <username>/kittygram_backend
sudo docker push <username>/kittygram_frontend
sudo docker push <username>/kittygram_gateway
```
Создать .env файл со следующим содержанием:
```shell
# DB
POSTGRES_USER=<user>
POSTGRES_PASSWORD=<password>
POSTGRES_DB=<django>
DB_HOST=db
DB_PORT=5432

# Django settings
SECRET_KEY=<django_secrt_key>
DEBUG=False
ALLOWED_HOSTS=127.0.0.1;localhost;

# Docker images 
BACKEND_IMAGE=<username>/kittygram_backend
FRONTEND_IMAGE=<username>/kittygram_frontend
GATEWAY_IMAGE=<username>/kittygram_gateway
```

💡 Дальнейшая инструкция предполагает, что удаленный сервер настроен на работу по `SSH`. 
На сервере установлен и настроен Docker и Nginx.

Перенести на удаленный сервер файлы `.env` `docker-compose.production.yml`:
```shell
scp /kittygram_final/.env /kittygram_final/docker-compose.production.yml <user@server-adres>:/<directory>
```
Подключиться к серверу:
```shell
ssh user@server-adres
```
Выполнить:
```shell
sudo docker compose -f docker-compose.production.yml up -d
```
Выполнить миграции:
```shell
sudo docker compose -f docker-compose.production.yml exec backend python manage.py migrate
```
Собрать статику:
```shell
sudo docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic
```
Скопировать статику:
```shell
sudo docker compose -f docker-compose.production.yml exec backend cp -r /app/collected_static/. /backend_static/static
```
Настроить шлюз для перенаправления запросов на `9000` порт, который слушает контейнер `kittygram_gateway`
```shell
sudo  vi /etc/nginx/sites-enabled/default
```
Пример конфигурации Nginx:
```shell
server {
    server_name  example.com;

    location / {
        client_max_body_size 20M;
        proxy_pass http://127.0.0.1:9000;
    }

    listen 443 ssl;
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
}

server {
    if ($host = example.com) {
        return 301 https://$host$request_uri;
    }

    listen 80;
    return 404;
    server_name example.com;
}
```

## GitHub Actions
Для использования автоматизированного развертывания и тестирования нужно 
изменить `.github/workflows/main.yml` под свои параметры и аккаунт.

Actions secrets:
- secrets.DOCKER_USERNAME
- secrets.DOCKER_PASSWORD
- secrets.HOST
- secrets.USER
- secrets.SSH_KEY
- secrets.SSH_PASSPHRASE
- secrets.TELEGRAM_TO
- secrets.TELEGRAM_TOKEN