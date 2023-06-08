# Kittygram

## Использованные технологии

![Docker](https://img.shields.io/badge/Docker-2496ed?style=for-the-badge&logo=docker&logoColor=white)![Postgres](https://img.shields.io/badge/Postgres-336791?style=for-the-badge&logo=Postgresql&logoColor=white)![Python](https://img.shields.io/badge/Python-30363d?style=for-the-badge&logo=Python&logoColor=yellow)![Django](https://img.shields.io/badge/Django-103e2e?style=for-the-badge&logo=Django&logoColor=white)![NodeJS](https://img.shields.io/badge/NodeJS-404137?style=for-the-badge&logo=Node.JS&logoColor=83cd29)

## Функционал сайта

После регистрации у вас есть возможность:

- Редактировать, добавлять, удалять, просматривать котов;
- Добавлять новые / присваивать существующие достижения;
- Просматривать чужих котов и их достижения;

## Подготовка к запуску

Установите Docker и Docker-compose на сервер.
```
sudo apt update
```
```
sudo apt install curl
``` 
```
curl -fSL https://get.docker.com -o get-docker.sh
```
```
sudo sh ./get-docker.sh
```
```
sudo apt-get install docker-compose-plugin
```

Клонируйте репозиторий:
```
git clone git@github.com:yomoe/kittygram_final.git
```
В папке с проектом переименуйте файл `.env.example` в `.env` и заполните его своими данными:

`POSTGRES_USER` - имя пользователя для базы данных

`POSTGRES_PASSWORD` - пароль для базы данных

`POSTGRES_DB` - имя базы данных

`SECRET_KEY` - секретный ключ для Django

`DEBUG` - режим отладки Django

`ALLOWED_HOSTS` - список разрешенных хостов

`DB_HOST` - имя хоста базы данных

`DB_PORT` - порт базы данных

`NGINX_PORT` - порт Nginx

`DOCKER_LOGIN` - логин от Docker Hub

## Запуск проекта

Находясь в папке с проектом запустите проект командой:
```
sudo docker compose -f docker-compose.production.yml up -d
```

Выполните миграции, соберите статические файлы бэкенда и скопируйте их в /backend_static/static/:

```
sudo docker compose -f docker-compose.production.yml exec backend python manage.py migrate
```
```
sudo docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic
```
```
sudo docker compose -f docker-compose.production.yml exec backend cp -r /app/collected_static/. /static/
```

Настройте Nginx:
```
sudo docker compose -f docker-compose.production.yml exec nginx nginx -s reload
```

Измените настройки location в секции server, вместо ХХХХХ укажите тот же порт, что и в .env файле `NGINX_PORT`:
```
location / {
    proxy_pass http://127.0.0.1:XXXXX;
}
```

Проверьте работоспособность конфига:
```
sudo nginx -t
```
Перезапустите Nginx:
```
sudo service nginx reload
```

## Настройка CI/CD
Файл workflow уже написан. Он находится в директории .github/workflows/main.yml
Для его работоспособности добавьте секреты в GitHub Actions:

`DOCKER_PASSWORD` - пароль от Docker Hub

`DOCKER_USERNAME` - логин от Docker Hub

`HOST` - ip адрес сервера

`USER` - имя пользователя на сервере

`SSH_KEY` - приватный ключ для подключения к серверу

`SSH_PASSPHRASE` - пароль от приватного ключа

`TELEGRAM_TO` - id пользователя в Telegram

`TELEGRAM_TOKEN` - токен бота в Telegram
