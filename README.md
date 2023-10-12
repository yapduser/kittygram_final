# Kittygram

## Технологии:

- Python
- Docker
- Django
- Gunicorn
- Nginx
- React
- DRF
- React

## Функционал:

- Проект Kittygram позволяет пользователям делиться своими фотографиями котиков и просматривать фотографии котиков других пользователей.
- При загрузке фото котика пользователь должен ввести в специальные поля имя котика, год его рождения и, при желании, может добавить ему достижения.
- Добавлять и просматривать фотографии могут только зарегистрированные и авторизованные пользователи.
- Только авторы могут изменять свои фотографии и описание.

##  Установка на локальный сервер

Клонировать репозиторий:
```shell
git clone git clone <https or SSH URL>
```

Перейдите в каталог проекта:
```shell
cd infra_sprint1/backend/
```

Настройте виртуальное окружение:

```shell
python3 -m venv venv
```

Обновить pip:
```shell
python3 -m pip install --upgrade pip
```

Установить зависимости:
```shell
pip install -r ../requirements.txt
```

Выполнить миграции:
```shell
python3 manage.py migrate
```