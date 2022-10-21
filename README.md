# Проект API_Yamdb с CI и CD 
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=3776AB&labelColor=C0C0C0)
![Django ](https://img.shields.io/badge/Django-092E20?style=for-the-badge&logo=Django&logoColor=092E20&labelColor=C0C0C0)
![Django REST Framework](https://img.shields.io/badge/Django-REST_Framework-092E20?style=for-the-badge&logo=Django&logoColor=092E20&labelColor=C0C0C0)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=Docker&logoColor=2496ED&labelColor=C0C0C0)
![Postgres](https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=PostgreSQL&logoColor=4169E1&labelColor=C0C0C0)
![Git](https://img.shields.io/badge/Git-F05032?style=for-the-badge&logo=GitHub&logoColor=F05032&labelColor=C0C0C0)
![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=GitHub&logoColor=181717&labelColor=C0C0C0)
![Yandex_Cloud](https://img.shields.io/badge/Yandex-Cloud-87CEFA?style=for-the-badge&labelColor=C0C0C0)
![.ENV](https://img.shields.io/badge/.ENV-ECD53F?style=for-the-badge&logo=.ENV&logoColor=ECD53F&labelColor=C0C0C0)

## Описание проекта
Проект собирает отзывы пользователей на произведения. Произведения делятся на категории. Список категорий может быть расширен.

##### Функционал:
###### REVIEWS
- Получить список всех отзывов
- Создать новый отзыв
- Получить отзыв по id
- Частично обновить отзыв по id
- Удалить отзыв по id
###### COMMENTS
- Получить список всех комментариев к отзыву по id
- Создать новый комментарий для отзыва
- Получить комментарий для отзыва по id
- Частично обновить комментарий к отзыву по id
- Удалить комментарий к отзыву по id
###### AUTH
- Отправление confirmation_code на переданный email
- Получение JWT-токена в обмен на email и confirmation_code
###### USERS
- Получить список всех пользователей
- Создание пользователя
- Получить пользователя по username
- Изменить данные пользователя по username
- Удалить пользователя по username
- Получить данные своей учетной записи
- Изменить данные своей учетной записи
###### CATEGORIES
- Получить список всех категорий
- Создать категорию
- Удалить категорию
###### GENRES
- Получить список всех жанров
- Создать жанр
- Удалить жанр
###### TITLES
- Получить список всех объектов
- Создать произведение для отзывов
- Информация об объекте
- Обновить информацию об объекте
- Удалить произведение

## Описание Workflow
##### Workflow состоит из четырёх шагов:
###### tests
- Проверка кода на соответствие PEP8, автоматический запуск тестов.
###### Push Docker image to Docker Hub
- Сборка и публикация образа на DockerHub.
###### deploy 
- Автоматический деплой на боевой сервер при пуше в главную ветку main.
###### send_massage
- Отправка уведомления в телеграм-чат.

## Подготовка и запуск проекта
##### Клонирование репозитория
Склонируйте репозиторий на локальную машину:
```bash
git clone git@github.com:DKDemerchyan/yamdb_final.git
```

## Установка на удаленном сервере (Ubuntu):
##### Шаг 1. Выполните вход на свой удаленный сервер
Прежде, чем приступать к работе, необходимо выполнить вход на свой удаленный сервер:
```bash
ssh <USERNAME>@<IP_ADDRESS>
```

##### Шаг 2. Установите docker на сервер:
Введите команду:
```bash
sudo apt install docker.io 
```

##### Шаг 3. Установите docker-compose на сервер:
Введите команды:
```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

##### Шаг 4. Скопируйте подготовленные файлы:
Скопируйте подготовленные файлы `docker-compose.yaml` и `nginx/default.conf` из вашего проекта на сервер в `home/<ваш_username>/docker-compose.yaml` и `home/<ваш_username>/nginx/default.conf` соответственно.
Введите команду из корневой папки проекта:
```bash
scp docker-compose.yml <username>@<host>:/home/<username>/docker-compose.yml
scp -r nginx/ <username>@<host>:/home/<username>/
```

##### Шаг 5. Workflow и .env:
Добавьте Workflow в Github и сделайте pull. Замените workflow-файл на имеющийся в проекте. В Secrets Guthub добавьте:
```bash
SECRET_KEY=<SECRET_KEY>
DEBUG=<True/False>
ALLOWED_HOSTS=<hosts>

DB_ENGINE=django.db.backends.postgresql
DB_NAME=postgres
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
DB_HOST=db
DB_PORT=5432

DOCKER_PASSWORD=<пароль DockerHub>
DOCKER_USERNAME=<имя пользователя DockerHub>

USER=<username для подключения к серверу>
HOST=<IP сервера>
PASSPHRASE=<пароль для сервера, если он установлен>
SSH_KEY=<ваш SSH ключ (для получения команда: cat ~/.ssh/id_rsa)>

TELEGRAM_TO=<ID своего телеграм-аккаунта>
TELEGRAM_TOKEN=<токен вашего бота>
```
В папку infra добавьте файл .env:
'''bash
DB_ENGINE=django.db.backends.postgresql
DB_NAME=postgres
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
DB_HOST=db
DB_PORT=5432
'''

##### Шаг 6. После успешного деплоя:
Зайдите на боевой сервер и выполните команды (только после первого деплоя):
###### Создаем и применяем миграции:
```bash
sudo docker-compose exec web python manage.py makemigrations --noinput
sudo docker-compose exec web python manage.py migrate --noinput
```
###### Подгружаем статику
```bash
sudo docker-compose exec web python manage.py collectstatic --no-input 
```
###### Заполнить базу данных:
```bash
sudo docker-compose exec web python3 manage.py loaddata fixtures.json
```
###### Создать суперпользователя Django:
```bash
sudo docker-compose exec web python manage.py createsuperuser
```

##### Шаг 7. Проект запущен
Проект будет доступен по вашему IP-адресу.
