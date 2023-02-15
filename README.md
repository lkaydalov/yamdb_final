![yamdb_final workflow](https://github.com/lkaydalov/yamdb_final/actions/workflows/yamdb_workflow.yml/badge.svg)

## Ссылка на развернутый проект
```
http://158.160.24.105/admin/
http://lkaydalov.serveblog.net/admin/
```

# Проект YaMDb
Сервис для сбора отзывов пользователей о различных произведениях.
## Описание
Пользователи сервиса могут составлять рейтинг произведений, путем оценки каждого оценкой от одного до десяти.
## Технологии
Python 3.7
Django 2.2.19
## Запуск проекта в контейнере
- Перенесите из проекта файлы docker-compose.yaml и папку nginx на сервер виртуальной машины
```
scp docker-compose.yaml <your_username>@<your_server_ip>:/home/<your_server_login>
scp -r nginx/ <your_username>@<your_server_ip>:/home/<your_server_login>
```
- Если на сервере уже запущен nginx - остановите его
```
sudo systemctl stop nginx
```
- Загрузите на сервер последний образ web
```
sudo docker pull <dockerhub_login>/<container_name>:<tag>
```
- Запустите 3 контейнера на основе образа (контейнер с сервером nginx, контейнер с образом postgresql, контейнер с проектом):
```
docker-compose up -d --build
```
- Теперь в контейнере web выполните миграции, создайте суперпользователя, соберите статику, и загрузите данные из БД
```
sudo docker-compose exec web python manage.py migrate
sudo docker-compose exec web python manage.py createsuperuser
sudo docker-compose exec web python manage.py collectstatic --no-input
sudo docker-compose exec web python manage.py loaddata fixtures.json
```
## Шаблон наполнения .env файла
```
SECRET_KEY=#задайте свой секретный ключ
DB_ENGINE=django.db.backends.postgresql 
DB_NAME=postgres 
POSTGRES_USER=postgres 
POSTGRES_PASSWORD=#задайте свой пароль 
DB_HOST=db 
DB_PORT=5432 
```
## Примеры API запросов-ответов
### Auth
##### Регистрация нового пользователя

- Запрос
    ```
    POST auth/signup/
    {
      "email": "string",
      "username": "string"
    }
    ```
- Ответ:
    ```
    {
      "email": "string",
      "username": "string"
    }
    ```
##### Получение JWT-токена
- Запрос
    ```
    POST auth/token/
    {
      "username": "string",
      "confirmation_code": "string"
    }
    ```
- Ответ 
    ```
    {
      "token": "string"
    }
    ```
### Categories
##### Получение списка всех категорий
- Запрос
    ```
    GET categories/
    ```
- Ответ
    ```
    [
      {
        "count": 0,
        "next": "string",
        "previous": "string",
        "results": [
          {
            "name": "string",
            "slug": "string"
          }
        ]
      }
    ]
    ```
##### Добавление новой категории
 - Запрос
    ```
    {
      "name": "string",
      "slug": "string"
    }
    ```
- Ответ
    ```
    {
      "name": "string",
      "slug": "string"
    }
    ```
##### Удаление категории
- Запрос
    ```
    DELETE categories/{slug}/
    ```
### Genres
##### Получение списка всех жанров
- Запрос
    ```
    GET genres/
    ```
- Ответ
    ```
    [
      {
        "count": 0,
        "next": "string",
        "previous": "string",
        "results": [
          {
            "name": "string",
            "slug": "string"
          }
        ]
      }
    ]
    ```
##### Добавление жанра
- Запрос
    ```
    POST genres/
    ```
- Ответ
    ```
    {
      "name": "string",
      "slug": "string"
    }
    ```
##### Удаление жанра
- Запрос
    ```
    DELETE genres/{slug}/
    ```
### TITLES
##### Получение списка всех произведений
- Запрос 
    ```
    GET titles/
    ```
- Ответ
    ```
   [
    {
    "count": 0,
    "next": "string",
    "previous": "string",
    "results": []
    }
    ]
    ```
##### Добавление произведения
- Запрос
    ```
    POST titles/
    {
    "name": "string",
    "year": 0,
    "description": "string",
    "genre": [
    "string"
    ],
    "category": "string"
    }
    ```
- Ответ
    ```
        {
    "id": 0,
    "name": "string",
    "year": 0,
    "rating": 0,
    "description": "string",
    "genre": [
    {}
    ],
    "category": {
    "name": "string",
    "slug": "string"
    }
    }
    ```
##### Получение информации о произведении
- Запрос
    ```
    GET titles/{titles_id}/
    ```
- Ответ
    ```
    {
    "id": 0,
    "name": "string",
    "year": 0,
    "rating": 0,
    "description": "string",
    "genre": [
    {}
    ],
    "category": {
    "name": "string",
    "slug": "string"
    }
    }
    ```
##### Частичное обновление информации о произведении
- Запрос
    ```
    PATCH titles/{titles_id}/
    ```
- Ответ
    ```
    {
      "name": "string",
      "year": 0,
      "description": "string",
      "genre": [
        "string"
      ],
      "category": "string"
    }
    ```
##### Удаление произведения
- Запрос 
    ```
    DELETE titles/{titles_id}/
    ```
### Reviews
##### Получение списка всех отзывов
- Запрос
    ```
    GET titles/{title_id}/reviews/
    ```
- Ответ
    ```
    [
    {
    "count": 0,
    "next": "string",
    "previous": "string",
    "results": []
    }
    ]
    ```
##### Добавление нового отзыва
- Запрос 
    ```
    POST titles/{title_id}/reviews/
    {
    "text": "string",
    "score": 1
    }
    ```
- Ответ
    ```
    {
    "id": 0,
    "text": "string",
    "author": "string",
    "score": 1,
    "pub_date": "2019-08-24T14:15:22Z"
    }
    ```
##### Полуение отзыва по id
- Запрос
    ```
    GET titles/{title_id}/reviews/{review_id}/
    ```
- Ответ
    ```
    {
    "id": 0,
    "text": "string",
    "author": "string",
    "score": 1,
    "pub_date": "2019-08-24T14:15:22Z"
    }
    ```
##### Частичное обновление отзыва по id
- Запрос 
    ```
    PATCH titles/{title_id}/reviews/{review_id}/
    {
    "text": "string",
    "score": 1
    }
    ```
- Ответ
    ```
    {
    "id": 0,
    "text": "string",
    "author": "string",
    "score": 1,
    "pub_date": "2019-08-24T14:15:22Z"
    }
    ```
##### Удаление отзыва по id
- Запрос
    ```
    DELETE titles/{title_id}/reviews/{review_id}/
    ```
### Comments
##### Получение списка всех комментариев к отзыву
- Запрос 
    ```
    GET titles/{title_id}/reviews/{review_id}/comments/
    ```
- Ответ
    ```
    [
    {
    "count": 0,
    "next": "string",
    "previous": "string",
    "results": []
    }
    ]
    ```
##### Добавление комментария к отзыву
- Запрос 
    ```
    POST titles/{title_id}/reviews/{review_id}/comments/
    {
    "text": "string"
    }
    ```
- Ответ
    ```
    {
    "id": 0,
    "text": "string",
    "author": "string",
    "pub_date": "2019-08-24T14:15:22Z"
    }
    ```
##### Получение комментария к отзыву
- Запрос
    ```
    GET titles/{title_id}/reviews/{review_id}/comments/{comment_id}/
    ```
- Ответ
    ```
    {
    "id": 0,
    "text": "string",
    "author": "string",
    "pub_date": "2019-08-24T14:15:22Z"
    }
    ```
##### Частичное обновление комментария к отзыву
- Запрос 
    ```
    PATCH titles/{title_id}/reviews/{review_id}/comments/{comment_id}/
    {
    "text": "string"
    }
    ```
- Ответ
    ```
    {
    "id": 0,
    "text": "string",
    "author": "string",
    "pub_date": "2019-08-24T14:15:22Z"
    }
    ```
##### Удаление комментария к отзыву
- Запрос
    ```
    DELETE titles/{title_id}/reviews/{review_id}/comments/{comment_id}/
    ```
### Users
##### Получение списка всех пользователей
- Запрос
    ```
    GET users/
    ```
- Ответ
    ```
    [
    {
    "count": 0,
    "next": "string",
    "previous": "string",
    "results": []
    }
    ]
    ```
##### Добавление пользователя
- Запрос
    ```
    POST users/
    {
    "username": "string",
    "email": "user@example.com",
    "first_name": "string",
    "last_name": "string",
    "bio": "string",
    "role": "user"
    }
    ```
- Ответ
    ```
    {
    "username": "string",
    "email": "user@example.com",
    "first_name": "string",
    "last_name": "string",
    "bio": "string",
    "role": "user"
    }
    ```
##### Получение пользователя по username
- Запрос
    ```
    GET users/{username}/
    ```
- Ответ
    ```
    {
    "username": "string",
    "email": "user@example.com",
    "first_name": "string",
    "last_name": "string",
    "bio": "string",
    "role": "user"
    }
    ```
##### Изменение данных пользователя по username
- Запрос 
    ```
    PATCH users/{username}/
    ```
- Ответ
    ```
    {
    "username": "string",
    "email": "user@example.com",
    "first_name": "string",
    "last_name": "string",
    "bio": "string",
    "role": "user"
    }
    ```
##### Удаление пользователя по username
- Запрос
    ```
    DELETE users/{username}/
    ```
##### Получение данных своей учетной записи
- Запрос
    ```
    GET users/me/
    ```
- Ответ
    ```
    {
    "username": "string",
    "email": "user@example.com",
    "first_name": "string",
    "last_name": "string",
    "bio": "string",
    "role": "user"
    }
    ```
##### Изменение данных своей учетной записи
- Запрос
    ```
    PATCH /users/me/
    {
    "username": "string",
    "email": "user@example.com",
    "first_name": "string",
    "last_name": "string",
    "bio": "string"
    }
    ```
- Ответ
    ```
    {
    "username": "string",
    "email": "user@example.com",
    "first_name": "string",
    "last_name": "string",
    "bio": "string",
    "role": "user"
    }
    ```

## Авторы
yandex-practikum, Андрей Грицков, Алексей Кайдалов, Елизавета Бойко.


### Авторы
yandex-practikum, Андрей Грицков, Алексей Кайдалов, Елизавета Бойко.