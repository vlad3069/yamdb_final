# Проект API YaMDb с применением CI/CD

[![API YaMDb Project CI/CD](https://github.com/vlad3069/yamdb_final/actions/workflows/yamdb_workflow.yml/badge.svg)](https://github.com/vlad3069/yamdb_final/actions/workflows/yamdb_workflow.yml)

## Описание

API YaMDb собирает отзывы пользователей на различные произведения такие как
фильмы, книги и музыка. Для приложения настроен Continuous Integration (CI) и
Continuous Deployment (CD).

Реализован:
* автоматический запуск тестов;
* обновление образов на DockerHub;
* автоматический деплой на боевой сервер при push-е в главную ветку main.

#### Доступный функционал

- Для аутентификации используются JWT-токены.
- У неаутентифицированных пользователей доступ к API только на уровне чтения.
- Создание объектов разрешено только аутентифицированным пользователям.На прочий фунционал наложено ограничение в виде административных ролей и авторства.
- Управление пользователями.
- Получение списка всех категорий и жанров, добавление и удаление.
- Получение списка всех произведений, их добавление.Получение, обновление и удаление конкретного произведения.
- Получение списка всех отзывов, их добавление.Получение, обновление и удаление конкретного отзыва.  
- Получение списка всех комментариев, их добавление.Получение, обновление и удаление конкретного комментария.
- Возможность получения подробной информации о себе и удаления своего аккаунта.
- Фильтрация по полям.


#### Технологии

- Python 3.8
- Django 3.2
- Django Rest Framework 3.12.4
- Simple JWT
- PostgreSQL 13.0-alpine
- Nginx 1.21.3-alpine
- Gunicorn 20.0.4
- Docker 20.10.17
- Docker-compose 3.8


#### Участники проекта

[vlad9603]Владислав Подтяжкин (teamlead) - управление пользователями (Auth и Users): система регистрации и аутентификации, права доступа, работа с токеном, система подтверждения e-mail, поля;

[urusaga]Галина Букреева - [https://github.com/urusaga](https://github.com/urusaga) - отзывы (Review) и комментарии (Comments): модели и view, эндпойнты, права доступа для запросов. Рейтинги произведений;

[Serg-Lugovski]Сергей Луговский - [https://github.com/Serg-Lugovski](https://github.com/Serg-Lugovski)- категории (Categories), жанры (Genres) и произведения (Titles): модели, view и эндпойнты для них;


## Начало работы

* Клонировать репозиторий, перейти в директорию с проектом:

git clone git@github.com:vlad3069/yamdb_final.git

cd api_yamdb


* Установить виртуальное окружение, активировать его:

python -m venv venv

. venv/scripts/activate


* Перейти в директорию с приложением ```api_yamdb```, установить зависимости:

pip install -r requirements.txt


* Для подключения GitHub Actions в ```api_yamdb```, необходимо создать директорию 
```.github/workflows``` и скопировать в неё файл ```yamdb_workflow.yml``` из
директории проекта.
* Для прохождения тестов, в директории ```infra```, создать файл ```.env``` с
переменными окружения:
```

ENGINE=django.db.backends.postgresql
DB_NAME                        # имя БД - postgres (по умолчанию)
POSTGRES_USER                  # логин для подключения к БД - postgres (по умолчанию)
POSTGRES_PASSWORD              # пароль для подключения к БД (установите свой)
DB_HOST=db                     # название сервиса (контейнера)
DB_PORT=5432                   # порт для подключения к БД

```
* В директории проекта ```api_yamdb```, запустить ```pytest```:
```
pytest
```

## Workflow

Для использования Continuous Integration (CI) и Continuous Deployment (CD): в
репозитории GitHub Actions ```Settings/Secrets/Actions``` прописать Secrets -
переменные окружения для доступа к сервисам:
```

DOCKER_USERNAME                # имя пользователя в DockerHub
DOCKER_PASSWORD                # пароль пользователя в DockerHub
HOST                           # ip_address сервера
USER                           # имя пользователя
SSH_KEY                        # приватный ssh-ключ (cat ~/.ssh/id_rsa)
PASSPHRASE                     # кодовая фраза (пароль) для ssh-ключа

TELEGRAM_TO                    # id телеграм-аккаунта (можно узнать у @userinfobot, команда /start)
TELEGRAM_TOKEN                 # токен бота (получить токен можно у @BotFather, /token, имя бота)
```
При push в ветку main автоматически отрабатывают сценарии:
* *tests* - проверка кода на соответствие стандарту PEP8 и запуск pytest.
Дальнейшие шаги выполняются только если push был в ветку main;
* *build_and_push_to_docker_hub* - сборка и доставка докер-образов на DockerHub
* *deploy* - автоматический деплой проекта на боевой сервер. Выполняется
копирование файлов из DockerHub на сервер;
* *send_message* - отправка уведомления в Telegram.
## Подготовка удалённого сервера
* Войти на удалённый сервер, для этого необходимо знать адрес сервера, имя
пользователя и пароль. Адрес сервера указывается по IP-адресу или по доменному
имени:
```
ssh <username>@<ip_address>
```
* Остановить службу ```nginx```:
```
sudo systemctl stop nginx
```
* Установить Docker и Docker-compose:
```
sudo apt update
sudo apt upgrade -y
sudo apt install docker.io
sudo apt install docker-compose -y
```
* Проверить корректность установки Docker-compose:
```
sudo docker-compose --version
```
* На сервере создать директорию ```nginx/templates/``` :
```
mkdir -p nginx/
```
* Скопировать файлы ```docker-compose.yaml``` и
```nginx/templates/default.conf``` из проекта (локально) на сервер в
```home/<username>/docker-compose.yaml``` и
```home/<username>/nginx/templates/default.conf``` соответственно:
  * перейти в директорию с файлом ```docker-compose.yaml``` и выполните:
  ```
  scp docker-compose.yaml <username>@<ip_address>:/home/<username>/docker-compose.yaml
  ```
  * перейти в директорию с файлом ```default.conf``` и выполните:
  ```
  scp default.conf <username>@<ip_address>:/home/<username>/nginx/default.conf
  ```
## После успешного деплоя

* Создать суперпользователя:
```
sudo docker-compose exec web python manage.py makemigrations

sudo docker-compose exec web python manage.py migrate

sudo docker-compose exec web python manage.py createsuperuser
```
* Для проверки работоспособности приложения, перейти на страницу:
```
http:/158.160.56.88/admin/
```
## Документация для YaMDb доступна по адресу:
```
http:/158.160.56.88/redoc/
```
