# Kittygram Backend

Backend-часть социальной сети Kittygram для обмена фотографиями котиков.

## Технологии

- Python 3.12
- Django 5.1.1
- Django REST Framework
- PostgreSQL
- Gunicorn
- Djoser

## Установка

### 1. Клонирование репозитория

```bash
git clone https://github.com/ваш_логин/kittygram_final.git && cd kittygram_final/
```

### 2. Настройка переменных окружения
Создайте файл .env в корне проекта (не в папке backend!):

```bash
nano .env
```

Минимальное содержимое:
```
# PostgreSQL
DB_ENGINE=postgresql
POSTGRES_DB=django
POSTGRES_USER=django_user
POSTGRES_PASSWORD=mysecretpassword
DB_HOST=db
DB_PORT=5432


# Django
SECRET_KEY='django-insecure-sample'
DEBUG=False
ALLOWED_HOSTS=localhost,158.160.223.19,hopto.hopto.org

# Docker
DOCKER_USERNAME=ad725
```
#### Если убрать переменную DB_ENGINE из файла .env, то backend запустится с SQLite.

### 3. Запуск бэкенда

```bash
# Запуск контейнеров
sudo docker compose -f docker-compose.production.yml up -d

# Применение миграций
sudo docker compose -f docker-compose.production.yml exec backend python manage.py migrate

# Сбор статики
sudo docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic --noinput

# Создание суперпользователя
sudo docker compose -f docker-compose.production.yml exec backend python manage.py createsuperuser
```

### 4. Примеры работы с API

### Аутентификация (Djoser)
Регистрация пользователя
```json
POST /api/users/
Content-Type: application/json
{
    "username": "Alex",
    "password": "securepass123",
    "email": "alex@mail.com"
}
```
Ответ (201 Created):

```json
{
    "id": 1,
    "username": "Alex",
    "email": "alex@mail.com"
}
```

### Получение токена
```json
POST /api/auth/token/login/
Content-Type: application/json
{
    "username": "Alex",
    "password": "securepass123"
}
```
Ответ (200 OK):
```json
{
    "auth_token": "9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b"
}
```

### Создание кота (с достижениями)
```json
POST /api/cats/
Authorization: Token 9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b
Content-Type: application/json
{
    "name": "Мурзик",
    "color": "#ffa500",
    "birth_year": 2021,
    "achievements": [
        {"achievement_name": "Любит спать"},
        {"achievement_name": "Любит играть"}
    ],
    "image": "data:image/jpeg;base64,/9j/4AAQSkZJRg..."  // опционально
}
```

Ответ (201 Created):
```json
{
    "id": 1,
    "name": "Мурзик",
    "color": "orange",
    "birth_year": 2021,
    "achievements": [
        {
            "id": 1,
            "achievement_name": "Любит спать"
        },
        {
            "id": 2,
            "achievement_name": "Любит играть"
        }
    ],
    "owner": 1,
    "age": 4,
    "image": null,
    "image_url": null
}
```

### Получение списка котов

```json
GET /api/cats/?page=1&page_size=2
Authorization: Token 9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b
```

Ответ (200 OK):
```json
{
    "count": 5,
    "next": "http://localhost:9000/api/cats/?page=2&page_size=2",
    "previous": null,
    "results": [
        {
            "id": 1,
            "name": "Мурзик",
            "color": "orange",
            "birth_year": 2021,
            "achievements": [
                {"id": 1, "achievement_name": "Любит спать"},
                {"id": 2, "achievement_name": "Любит играть"}
            ],
            "owner": 1,
            "age": 4,
            "image": null,
            "image_url": null
        },
        {
            "id": 2,
            "name": "Барсик",
            "color": "gray",
            "birth_year": 2022,
            "achievements": [],
            "owner": 1,
            "age": 3,
            "image": null,
            "image_url": null
        }
    ]
}
```