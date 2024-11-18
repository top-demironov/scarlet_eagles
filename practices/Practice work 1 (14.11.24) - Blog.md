
# Задание №1: Создание простого блога на Django

## Цель
Познакомиться с основами Django: установка, настройка проекта, создание приложений, моделей, баз данных и базовых представлений.

---

## Шаги выполнения задания

### 1. Установите Django и создайте проект
- Установите Django с помощью команды:
  ```bash
  pip install django
  ```
- Создайте новый проект с именем `myblog`:
  ```bash
  django-admin startproject myblog
  ```

### 2. Создайте приложение
- Внутри проекта создайте приложение `blog`:
  ```bash
  python manage.py startapp blog
  ```

### 3. Настройте приложение
- В файле `settings.py` добавьте приложение `blog` в список `INSTALLED_APPS`.

### 4. Создайте модель для постов блога
- В файле `blog/models.py` опишите модель `Post` с полями:
  - `title` (заголовок поста, строка до 200 символов).
  - `content` (текст поста).
  - `published_date` (дата публикации).

### 5. Примените миграции
- Создайте миграции и примените их:
  ```bash
  python manage.py makemigrations
  python manage.py migrate
  ```

### 6. Создайте админ-панель для модели
- В файле `blog/admin.py` зарегистрируйте модель `Post`:
  ```python
  from django.contrib import admin
  from .models import Post

  admin.site.register(Post)
  ```

### 7. Настройте маршруты (URLs)
- В файле `blog/urls.py` создайте маршруты для просмотра списка постов:
  ```python
  from django.urls import path
  from . import views

  urlpatterns = [
      path('', views.post_list, name='post_list'),
  ]
  ```
- Свяжите маршруты приложения с проектом в `myblog/urls.py`:
  ```python
  from django.contrib import admin
  from django.urls import path, include

  urlpatterns = [
      path('admin/', admin.site.urls),
      path('', include('blog.urls')),
  ]
  ```

### 8. Создайте базовое представление
- В файле `blog/views.py` добавьте функцию для отображения списка постов:
  ```python
  from django.shortcuts import render
  from .models import Post

  def post_list(request):
      posts = Post.objects.all()
      return render(request, 'blog/post_list.html', {'posts': posts})
  ```

### 9. Создайте шаблон
- В папке `blog/templates/blog/` создайте файл `post_list.html`:
  ```html
  <!DOCTYPE html>
  <html>
  <head>
      <title>My Blog</title>
  </head>
  <body>
      <h1>Blog Posts</h1>
      <ul>
          {% for post in posts %}
              <li>
                  <h2>{{ post.title }}</h2>
                  <p>{{ post.content }}</p>
                  <p><small>Published on {{ post.published_date }}</small></p>
              </li>
          {% endfor %}
      </ul>
  </body>
  </html>
  ```

### 10. Запустите сервер и проверьте результат
- Запустите сервер разработки:
  ```bash
  python manage.py runserver
  ```
- Перейдите по адресу [http://127.0.0.1:8000/](http://127.0.0.1:8000/) и убедитесь, что блог отображается корректно.

---

## Результат
Каждый участник должен создать проект с базовым функционалом блога, где можно увидеть список постов.

---

## Дополнительно (по желанию)
- Добавить возможность создания постов через админку.
- Использовать стилизацию (CSS) для улучшения внешнего вида блога.
