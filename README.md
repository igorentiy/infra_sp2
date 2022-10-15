# api_yamdb

## Стек технологий: Python 3, Django 2.2, PostgreSQL, Docker, gunicorn, nginx, Яндекс.Облако (Ubuntu 18.04), Unittest.

## Описание
Проект **YaMDb** собирает отзывы пользователей на различные произведения.

### Используется пагинация постов и кэширование. Реализована регистрация пользователей с верификацией данных, сменой и восстановлением пароля через почту. Написаны тесты, проверяющие работу сервиса

### Запуск приложения:
``` git clone git@github.com:igorentiy/infra_sp2.git  ``` \
```docker-compose up```

### Выполнить миграции:
```docker-compose exec web python manage.py migrate```

### Создать суперпользователя:
```docker-compose exec web python manage.py createsuperuser```

### Привязать статические файлы:
```docker-compose exec web python manage.py collectstatic --no-input```

### Заполнить базу начальными данными:
```docker-compose exec web python manage.py loaddata db.json```

### Алгоритм регистрации пользователей
1. Пользователь отправляет POST-запрос на добавление нового пользователя с параметрами `email` и `username` на эндпоинт `/api/v1/auth/signup/`.
2. **YaMDB** отправляет письмо с кодом подтверждения (`confirmation_code`) на адрес `email`.
3. Пользователь отправляет POST-запрос с параметрами `username` и `confirmation_code` на эндпоинт `/api/v1/auth/token/`, в ответе на запрос ему приходит `token` (JWT-токен).
4. При желании пользователь отправляет PATCH-запрос на эндпоинт `/api/v1/users/me/` и заполняет поля в своём профайле (описание полей — в документации).

### Пользовательские роли
* **Аноним** — может просматривать описания произведений, читать отзывы и комментарии.
* **Аутентифицированный пользователь** (`user`) — может, как и **Аноним**, читать всё, дополнительно он может публиковать отзывы и ставить оценку произведениям (фильмам/книгам/песенкам), может комментировать чужие отзывы; может редактировать и удалять **свои** отзывы и комментарии. Эта роль присваивается по умолчанию каждому новому пользователю.
* **Модератор** (`moderator`) — те же права, что и у **Аутентифицированного пользователя** плюс право удалять **любые** отзывы и комментарии.
* **Администратор** (`admin`) — полные права на управление всем контентом проекта. Может создавать и удалять произведения, категории и жанры. Может назначать роли пользователям.
* **Суперюзер Django** — обладет правами администратора (`admin`)
