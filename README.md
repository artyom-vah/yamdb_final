## Проект «yamdb_final» CI/CD для API YAMDB


### Описание
[![yamdb_final](https://github.com/artyom-vah/yamdb_final/actions/workflows/yamdb_workflow.yml/badge.svg)](https://github.com/artyom-vah/yamdb_final/actions)

   Проект YaMDb собирает отзывы пользователей на произведения.  
   Сами произведения в YaMDb не хранятся, здесь нельзя посмотреть фильм или послушать музыку.<br/>    
   Каждый ресурс описан в документации: указаны эндпоинты (адреса, по которым можно сделать запрос),    
разрешённые типы запросов, права доступа и дополнительные параметры, когда это необходимо.<br/>    
   Добавлять произведения, категории и жанры может только администратор.<br/>    
   Благодарные или возмущённые пользователи оставляют к произведениям текстовые отзывы  
и ставят произведению оценку в диапазоне от одного до десяти (целое число);  
из пользовательских оценок формируется усреднённая оценка произведения — рейтинг (целое число).<br/>  
   Добавлять отзывы, комментарии и ставить оценки могут только аутентифицированные пользователи.<br/>    

## Workflow
* tests - Проверка кода на соответствие стандарту PEP8 (с помощью пакета flake8) и запуск pytest. Дальнейшие шаги выполнятся только если push был в ветку master или main.
* build_and_push_to_docker_hub - Сборка и доставка докер-образов на Docker Hub
* deploy - Автоматический деплой проекта на боевой сервер. Выполняется копирование файлов из репозитория на сервер:
* send_message - Отправка уведомления в Telegram

### Технологии:
![Python](https://img.shields.io/badge/Python-FFD43B?style=for-the-badge&logo=python&logoColor=blue)
![Django](https://img.shields.io/badge/Django-092E20?style=for-the-badge&logo=django&logoColor=green)
![DRF](https://img.shields.io/badge/django%20rest-ff1709?style=for-the-badge&logo=django&logoColor=white)
![JWT](https://img.shields.io/badge/JWT-000000?style=for-the-badge&logo=JSON%20web%20tokens&logoColor=white)

### Используемые пакеты:    
    * Django==3.2
    * djangorestframework==3.12.4
    * requests==2.26.0
    * PyJWT==2.1.0
    * pytest==6.2.4
    * pytest-django==4.4.0
    * pytest-pythonpath==0.7.3
    * djangorestframework-simplejwt==5.2.2
    * django-filter==22.1
    * python-dotenv==0.21.1

### Установка

1. Клонировать репозиторий:

   ```python
   git clone ...
   ```

2. Перейти в папку с проектом:

   ```python
   cd api_yamdb/
   ```

3. Установить виртуальное окружение для проекта:

   ```python
   python -m venv venv
   ```

4. Активировать виртуальное окружение для проекта:

   ```python
   # для OS Lunix и MacOS
   source venv/bin/activate
   # для OS Windows
   source venv/Scripts/activate
   ```

5. Установить зависимости:

   ```python
   python -m pip install --upgrade pip
   pip install -r requirements.txt
   ```

6. Выполнить миграции на уровне проекта:

   ```python
   cd api_yamdb
   python3 manage.py migrate
   ```

7. Запустить проект:
   ```python
   python manage.py runserver
   ```

В репозитории на Гитхабе добавьте данные в `Settings - Secrets - Actions secrets`:
```
DOCKER_USERNAME - имя пользователя в DockerHub
DOCKER_PASSWORD - пароль пользователя в DockerHub
HOST - ip-адрес сервера
USER - пользователь
SSH_KEY - приватный ssh-ключ (публичный должен быть на сервере)
PASSPHRASE - кодовая фраза для ssh-ключа
DB_ENGINE - django.db.backends.postgresql
DB_HOST - db
DB_PORT - 5432
SECRET_KEY - секретный ключ приложения django (необходимо чтобы были экранированы или отсутствовали скобки)
ALLOWED_HOSTS - список разрешённых адресов
TELEGRAM_TO - id своего телеграм-аккаунта (можно узнать у @userinfobot, команда /start)
TELEGRAM_TOKEN - токен бота (получить токен можно у @BotFather, /token, имя бота)
DB_NAME - postgres (по умолчанию)
POSTGRES_USER - postgres (по умолчанию)
POSTGRES_PASSWORD - postgres (по умолчанию)
```


### Дополнительно

## Как запустить проект на сервере:

Установите Docker и Docker-compose:
```
sudo apt install docker.io
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```
Проверьте корректность установки Docker-compose:
```
sudo  docker-compose --version
```
Создайте папку `nginx`:
```
mkdir nginx
```
### После успешного деплоя:
Соберите статические файлы (статику):
```
docker-compose exec web python manage.py collectstatic --no-input
```
Примените миграции:
```
docker-compose exec web python manage.py migrate
docker-compose exec web python manage.py makemigrations
docker-compose exec web python manage.py migrate --noinput
```
Создайте суперпользователя:
```
docker-compose exec web python manage.py createsuperuser

```
или
```
docker-compose exec web python manage.py loaddata fixtures.json
```

### Примеры запросов

* Пример POST-запроса<br/>   
    Регистрация нового пользователя и получение `confirmation_code`. Доступно без токена.  
    `POST http://127.0.0.1:8000/api/v1/auth/signup/`
    ```json
    {
        "email": "user@example.com",
        "username": "string"
    }
    ```
* Пример ответа:
    ```json
    {
        "email": "string",
        "username": "string"
    }
    ```
  В проекте настроен filebased способ отправки почты, confirmation_code будет находится в папке send_email базовой директории.
* Получение JWT-токена в обмен на `username` и `confirmation_code`. Доступно без токена.  
    `POST http://127.0.0.1:8000/api/v1/auth/token/`
    ```json
    {
        "username": "string",
        "confirmation_code": "string"
    }
    ```
* Пример ответа:
    ```json
    {
        "token": "string"
    }
    ```
  В дальнейшем token передаётся в Header: Bearer
* Создание отзыва к произведению. Необходим токен.  
    `POST http://127.0.0.1:8000/api/v1/titles/{title_id}/reviews/`
    ```json
    {
        "text": "string",
        "score": 1
    }
    ```
* Пример ответа:
    ```json
    {
        "id": 0,
        "text": "string",
        "author": "string",
        "score": 1,
        "pub_date": "2019-08-24T14:15:22Z"
    }
    ```

### Авторы проекта
* Артем Вахрушев  
* Роман Дячук  
* Руслан Шамсияров  
