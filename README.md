Kittygram

Kittygram — веб-приложение для публикации фотографий котиков, информации о них и их достижениях.

Технологии

* Python 3.12
* Django 5.1.1
* Django REST Framework
* React
* PostgreSQL 13
* Gunicorn
* Nginx
* Docker
* Docker Compose
* GitHub Actions
* Ruff

Архитектура проекта

Проект состоит из четырёх сервисов:

* backend — серверная часть приложения;
* frontend — пользовательский интерфейс;
* db — база данных PostgreSQL;
* gateway — веб-сервер Nginx.

Для постоянного хранения данных используются тома pg_data, static и media.

Локальный запуск

Клонировать репозиторий:

git clone https://github.com/BigLita7/kittygram_final.git
cd kittygram_final

Создать файл переменных окружения:

cp .env.example .env

Заполнить необходимые переменные окружения в файле .env.

Собрать и запустить контейнеры:

docker compose up -d --build

Выполнить миграции:

docker compose exec backend python manage.py migrate

Собрать статические файлы:

docker compose exec backend python manage.py collectstatic --noinput
docker compose exec backend cp -r /app/collected_static/. /backend_static/

Создать администратора:

docker compose exec backend python manage.py createsuperuser

Приложение доступно по адресу http://localhost:9000.

Панель администратора доступна по адресу http://localhost:9000/admin/.

Тестирование

Проверка качества Python-кода:

ruff check backend/

Тестирование backend:

docker compose exec backend python manage.py test

Тестирование frontend:

docker compose run --rm --no-deps -e CI=true frontend npm test -- --watchAll=false

CI/CD

При отправке изменений в ветку main GitHub Actions автоматически выполняет:

1. Проверку Python-кода через Ruff.
2. Тестирование backend.
3. Тестирование frontend.
4. Сборку Docker-образов.
5. Публикацию образов в Docker Hub.

Публикация выполняется только после успешного прохождения проверок.

Запуск готовых образов

Для использования образов из Docker Hub предусмотрен файл docker-compose.production.yml.

docker compose -f docker-compose.production.yml pull
docker compose -f docker-compose.production.yml up -d

При первом запуске необходимо выполнить миграции и сборку статических файлов, указав соответствующий файл Docker Compose.

Остановка проекта

docker compose down