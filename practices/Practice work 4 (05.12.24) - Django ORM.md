# Классная работа – Модели и ORM в Django

---

## Цель занятия

1. Изучить работу с моделями в Django.
2. Научиться создавать и управлять базой данных через ORM.
3. Разобраться с миграциями и их применением.
4. Понять основные типы данных и связей между моделями.

---

## **Введение**

Модели в Django позволяют:
- Описывать структуру базы данных через Python-классы.
- Использовать Django ORM для взаимодействия с базой данных без написания SQL-запросов.
- Автоматически управлять изменениями базы данных через миграции.

---

## **1. Теория: Что такое модель и ORM?**

### **Модель**
Модель — это Python-класс, который описывает таблицу базы данных.  
Каждое поле модели соответствует столбцу таблицы.  

### **Django ORM**
ORM (Object-Relational Mapping) — это инструмент, который:
- Позволяет работать с данными в базе через Python-код.
- Упрощает выполнение операций, таких как создание, чтение, обновление и удаление записей.

### **Миграции**
Миграции управляют изменениями структуры базы данных, чтобы она соответствовала описанию моделей.

---

## **2. Типы данных в моделях Django**

Django предоставляет широкий выбор полей для описания структуры базы данных:

### **Строковые поля**
- `CharField(max_length=...)` — строка с ограничением по длине (обязательно указывать `max_length`).
- `TextField()` — текстовое поле без ограничения длины.
- `SlugField()` — строка для URL-адресов (например, "my-article").

### **Числовые поля**
- `IntegerField()` — целое число.
- `BigIntegerField()` — большое целое число.
- `FloatField()` — число с плавающей точкой.
- `DecimalField(max_digits=..., decimal_places=...)` — число с фиксированной точностью.

### **Логические поля**
- `BooleanField()` — булево значение (True/False).
- `NullBooleanField()` — булево значение, которое может быть `null`.

### **Дата и время**
- `DateField()` — дата.
- `TimeField()` — время.
- `DateTimeField()` — дата и время.
- `AutoField()` — автоинкрементное поле для первичного ключа.
- `DurationField()` — интервал времени.

### **Файлы и изображения**
- `FileField()` — поле для загрузки файлов.
- `ImageField()` — поле для загрузки изображений (требует Pillow).

### **Другие типы**
- `EmailField()` — строка для email-адресов.
- `URLField()` — строка для URL-адресов.
- `UUIDField()` — поле для уникальных идентификаторов (UUID).
- `JSONField()` — поле для хранения JSON-данных (требует PostgreSQL или других совместимых СУБД).

---

## **3. Типы связей между моделями**

Django поддерживает три основных типа связей между моделями:

### **1. Связь "Один ко многим" (`ForeignKey`)**
- Используется, когда одна запись в одной таблице может быть связана с несколькими записями в другой таблице.  
- Пример: Один автор может иметь несколько статей.

**Пример модели:**
```python
from django.db import models

class Author(models.Model):
    name = models.CharField(max_length=100)

class Article(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
```

**Объяснение:**
- Поле author в модели `Article` указывает на модель `Author`.
- `on_delete=models.CASCADE` означает, что при удалении автора, все связанные статьи будут удалены.

**Работа с данными:**
```python
# Создаем автора
author = Author.objects.create(name="John Doe")

# Создаем статью, связанную с автором
article = Article.objects.create(title="Первая статья", content="Текст", author=author)

# Получаем все статьи автора
articles_by_author = Article.objects.filter(author=author)
```

---

### **2. Связь "Многие ко многим" (`ManyToManyField`)**
- Используется, когда одна запись в одной таблице может быть связана с несколькими записями в другой таблице, и наоборот.
- Пример: Статьи могут быть связаны с несколькими категориями, а категории — с несколькими статьями.

**Пример модели:**
```python
class Category(models.Model):
    name = models.CharField(max_length=100)

class Article(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    categories = models.ManyToManyField(Category)
```

**Объяснение:**
- Поле categories в модели `Article` указывает на связь "многие ко многим" с моделью `Category`.

**Работа с данными:**
```python
# Создаем категории
category1 = Category.objects.create(name="Новости")
category2 = Category.objects.create(name="Спорт")

# Создаем статью
article = Article.objects.create(title="Первая статья", content="Текст")

# Добавляем категории к статье
article.categories.add(category1, category2)

# Получаем все статьи, относящиеся к категории "Новости"
articles_in_news = Article.objects.filter(categories=category1)
```

---

### **3. Связь "Один к одному" (`OneToOneField`)**
- Используется, когда одна запись в одной таблице может быть связана только с одной записью в другой таблице.
- Пример: У пользователя может быть только один профиль.

**Пример модели:**
```python
class User(models.Model):
    username = models.CharField(max_length=100)

class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    bio = models.TextField()
```

**Объяснение:**
- Поле `user` в модели `Profile` указывает на связь "один к одному" с моделью `User`.

**Работа с данными:**
```python
# Создаем пользователя
user = User.objects.create(username="johndoe")

# Создаем профиль, связанный с пользователем
profile = Profile.objects.create(user=user, bio="Биография пользователя")

# Получаем профиль пользователя
user_profile = Profile.objects.get(user=user)
```

## Дополнительные источники
1. https://developer.mozilla.org/ru/docs/Learn/Server-side/Django/Models

---

## **4. Практика: Разработка системы задач и прогресса**
### **Цель задания**
1. Закрепить знания о моделях и связях между ними.
2. Научиться использовать отношения "Один ко многим" и "Многие ко многим".
3. Создать систему управления задачами с отслеживанием их статуса и прогресса.

### **Описание задания**

Вы разрабатываете систему для управления задачами. Она должна включать:
1. Создание проектов.
2. Добавление задач в проекты.
3. Возможность назначения нескольких исполнителей для каждой задачи.
4. Отслеживание статуса задач (новая, в процессе, завершена).

### **Требования к выполнению**
#### **Модели**

Модель `Project` (Проект):

- Поля:
  - `name` (название проекта).
  - `description` (описание проекта).
  - `created_at` (дата создания, автоматическая).

Модель `Task` (Задача):

- Поля:
  - `title` (название задачи).
  - `description` (описание задачи).
  - `status` (статус задачи: новая, в процессе, завершена).
  - `created_at` (дата создания, автоматическая).
  - `due_date` (дата завершения задачи).
- Связь:
  - Один ко многим с моделью `Project`.
  - Многие ко многим с моделью `User`.

Модель `User` (Исполнитель):

- Поля:
  - `username` (имя пользователя).
  - `email` (электронная почта).

    

#### **HTML шаблоны**
1. Шаблоны должны наследоваться от `base.html`
2. Вынести `header` и `footer` в отдельный шаблон `header.html`
3. Использование `bootstrap`

---

### **Этапы выполнения**
#### **0. Создайте виртуальную среду**
- Создайте виртуальную среду, установите нужные библиотеки, после установки библиотек не забудьте сформировать `requirements.txt`.

#### **1. Создайте проект Django**
- Назовите проект `taskmanager`.

#### **2. Создайте приложение**
- Внутри проекта создайте приложение `tasks`.

#### **3. Настройте приложение**
- Добавьте `tasks` в список `INSTALLED_APPS` в файле `settings.py`.

#### **4. Создайте модели в файле `models.py`**
- Создайте модели `Project`, `Task` и `User` с указанными полями и связями.

В файле `taskmanager/tasks/models.py`
```python
from django.db import models


class Project(models.Model):
  name = models.CharField(max_length=100)
  description = models.TextField()
  created_at = models.DateTimeField(auto_now_add=True)
  
  def __str__(self):
      return self.name


class User(models.Model):
  username = models.CharField(max_length=100)
  email = models.EmailField()
  
  def __str__(self):
      return self.username


class Task(models.Model):
  title = models.CharField(max_length=100)
  description = models.TextField()
  status = models.CharField(max_length=100)
  created_at = models.DateTimeField(auto_now_add=True)
  due_date = models.DateField()
  project = models.ForeignKey(Project, on_delete=models.CASCADE)
  performers = models.ManyToManyField(User)
  
  def __str__(self):
      return f'{self.title} ({self.status})'
```

#### **5. Зарегистрируйте модели в админ-панели**
- Зарегистрируйте модели `Project`, `Task` и `User` в админ панели в файле `admin.py`.

В файле `taskmanager/tasks/admin.py`
```python
from django.contrib import admin

from tasks.models import Project, Task, User


admin.site.register(Project)
admin.site.register(Task)
admin.site.register(User)
```

#### **6. Создайте супер-пользователя для доступа в админ-панель**
- В консоли создайте супер-пользователя командой `createsuperuser`.

#### **7. Создайте `Project`, `Task`, `User` в админ-панели**
- Создайте какой-нибудь проект, исполнителей, и задачи для этого проекта в админ-панели

#### **8. Создайте главную страницу проекта**
- Создайте файл `base.html`, подключите в нем `bootstrap`
- Создайте файл `index.html`, унаследуйте его у шаблона `base.html`
- Создайте `header.html`, подключите его в шаблоне `base.html`
- Создайте `footer.html`, подключите его в шаблоне `base.html`
- На главной странице отобразите описание сайта

В файле `taskmanager/templates/base.html`
```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Система управления проектами</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
</head>
<body>

  {% include 'includes/header.html' %} <!-- Подключаем header -->

  <section class="container py-5" style="min-height: calc(100vh - 56px - 56px);">
    {% block content %}{% endblock %} <!-- Здесь будет контент каждого шаблона -->
  </section>

  {% include 'includes/footer.html' %}

  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz" crossorigin="anonymous"></script>
</body>
</html>
```

В файле `taskmanager/templates/includes/header.html`
```html
<header>
  <nav class="navbar navbar-expand-lg bg-body-tertiary">
    <div class="container-fluid">
      <a class="navbar-brand" href="#">Система управления проектами</a>
      <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarNav">
        <ul class="navbar-nav">
          <li class="nav-item">
            <a class="nav-link" href="#">Проекты</a>
          </li>
          <li class="nav-item">
            <a class="nav-link" href="#">Исполнители</a>
          </li>
          <li class="nav-item">
            <a class="nav-link" href="#">Задачи</a>
          </li>
        </ul>
      </div>
    </div>
  </nav>
</header>
```

В файле `taskmanager/templates/includes/footer.html`
```html
<footer class="bg-body-tertiary p-3">
  <div class="d-flex justify-content-between">
    <span>Создано в целях обучения</span>
    <span>Миронов Даниил</span>
  </div>
</footer>
```

В файле `taskmanager/templates/tasks/index.html`
```html
{% extends 'base.html' %}

{% block content %}
  <h1>Система управления проектами</h1>
  <p class="mb-5 text-body-secondary">В современном мире, где проекты становятся всё более сложными и многозадачными, 
      использование системы управления проектами становится необходимостью для успешного выполнения задач. Она 
      помогает командам эффективно работать над проектами, управлять ресурсами и достигать поставленных целей в 
      установленные сроки.
  </p>
  <div class="row flex-column gap-4 align-items-center">
    <div class="col-12">
      <div class="card">
        <div class="card-body">
          <h5 class="card-title">Создайте проект</h5>
          <h6 class="card-subtitle mb-2 text-body-secondary">Позволит проще управлять</h6>
          <p class="card-text">Проект должен нести определенный смысл, в него будут добавляться задачи, все структурировано</p>
          <a href="#" class="card-link">Создать проект</a>
        </div>
      </div>
    </div>
    <div class="col-12">
      <div class="card">
        <div class="card-body">
          <h5 class="card-title">Создайте исполнителей проекта</h5>
          <h6 class="card-subtitle mb-2 text-body-secondary">Разделяй и управляй</h6>
          <p class="card-text">Каждой задаче требуется ответственный исполнитель, иначе она просто не будет выполняться</p>
          <a href="#" class="card-link">Создать исполнителя</a>
        </div>
      </div>
    </div>
    <div class="col-12">
      <div class="card">
        <div class="card-body">
          <h5 class="card-title">Создайте задачи для проекта</h5>
          <h6 class="card-subtitle mb-2 text-body-secondary">Декомпозируйте что-то большое</h6>
          <p class="card-text">Всем известно, что слона проще есть по частям</p>
          <a href="#" class="card-link">Создать задачу</a>
        </div>
      </div>
    </div>
    <div class="col-12">
      <a href="#">Взглянуть на проекты</a>
    </div>
  </div>
{% endblock %}
```

#### **9. Подключим шаблон отображения в файлах `views`, `urls`**
В файле `taskmanager/tasks/views.py`
```python
from django.shortcuts import render


def index(request):
  return render(request, 'index.html')
```

В файле `taskmanager/tasks/urls.py`
```python
from django.urls import path

from tasks import views


urlpatterns = [
  path('', views.index, name='index'),
]
```

В файле `taskmanager/taskmanager/urls.py`
```python
from django.contrib import admin
from django.urls import path, include


urlpatterns = [
  path('admin/', admin.site.urls),
  path('', include('tasks.urls')),
]
```

#### **10. По аналогии создадим страницу проектов**
В файле `taskmanager/templates/tasks/projects.html`
```html
{% extends 'base.html' %}

{% block content %}
  <h1>Список проектов</h1>
  <p class="mb-5 text-body-secondary">Все проекты в системе</p>
  <div class="row flex-column gap-4 align-items-center">
    {% for project in projects %}
      <div class="col-12">
        <div class="card">
          <div class="card-body">
            <h5 class="card-title">{{ project.name }}</h5>
            <h6 class="card-subtitle mb-2 text-body-secondary">{{ project.created_at }}</h6>
            <pre class="card-text">{{ project.description }}</pre>
            <a href="#" class="card-link">Посмотреть проект</a>
          </div>
        </div>
      </div>
    {% empty %}
      <div class="col-12">
        <div class="card">
          <div class="card-body">
            <h5 class="card-title">Еще нет проектов</h5>
            <h6 class="card-subtitle mb-2 text-body-secondary">Создайте что-бы улучшить свой проект-менеджмент</h6>
            <p class="card-text">Проект должен нести определенный смысл, в него будут добавляться задачи, все структурировано</p>
            <a href="#" class="card-link">Создать проект</a>
          </div>
        </div>
      </div>
    {% endfor %}
  </div>
{% endblock %}
```

В файле `taskmanager/tasks/urls.py` добавим
```python
urlpatterns = [
  ...
  path('projects/', views.projects, name='projects'),
]
```

В файле `taskmanager/tasks/views.py` добавим
```python
...

def projects(request):
  projects_list = Project.objects.all()
  return render(request, 'tasks/projects.html', context={'projects': projects_list})
```

Незабываем изменить ссылки, во всех местах, где мы хотим ссылаться на страницу всех проектов.

В файле `taskmanager/includes/header.html` изменим
```html
...
<div class="container-fluid">
  <a class="navbar-brand" href="{% url 'index' %}">Система управления проектами</a>
  
...

<div class="collapse navbar-collapse" id="navbarNav">
  <ul class="navbar-nav">
    <li class="nav-item">
      <a class="nav-link" href="{% url 'projects' %}">Проекты</a>
    </li>
    <li class="nav-item">
      <a class="nav-link" href="#">Исполнители</a>
    </li>
...
```

В файле `taskmanager/templates/tasks/index.html` изменим
```html
...
  </div>
  <div class="col-12">
    <a href="{% url 'projects' %}">Взглянуть на проекты</a>
  </div>
</div>
{% endblock %}
```

#### **11. Также создадим страницу для вывода всех исполнителей**
В файле `taskmanager/templates/tasks/performers.html`
```html
{% extends 'base.html' %}

{% block content %}
  <h1>Список исполнителей</h1>
  <p class="mb-5 text-body-secondary">Каждый исполнитель может участвовать в любой задачи из любого проекта</p>
  <div class="row ">
    {% for performer in performers %}
      <div class="col-6">
        <div class="card">
          <div class="card-body">
            <h5 class="card-title">{{ performer.username }}</h5>
            <h6 class="card-subtitle mb-2 text-body-secondary">{{ performer.email}}</h6>
          </div>
        </div>
      </div>
    {% empty %}
      <div class="col-12">
        <div class="card">
          <div class="card-body">
            <h5 class="card-title">Еще нет исполнителей</h5>
            <h6 class="card-subtitle mb-2 text-body-secondary">Если вы работаете один, то вы либо дурак, либо сверхчеловек</h6>
            <p class="card-text">Каждой задаче требуется ответственный исполнитель, иначе она просто не будет выполняться</p>
            <a href="#" class="card-link">Создать исполнителя</a>
          </div>
        </div>
      </div>
    {% endfor %}
  </div>
{% endblock %}
```

В файле `taskmanager/tasks/urls.py` добавим
```python
urlpatterns = [
  ...
  path('performers/', views.performers, name='performers'),
]
```

В файле `taskmanager/tasks/views.py` добавим
```python
...

def performers(request):
  performers_list = User.objects.all()
  return render(request, 'tasks/performers.html', context={'performers': performers_list})
```

Незабываем изменить ссылки, во всех местах, где мы хотим ссылаться на страницу всех проектов.

В файле `taskmanager/includes/header.html` изменим
```html
...
<div class="container-fluid">
  <a class="navbar-brand" href="{% url 'index' %}">Система управления проектами</a>
  
...

<div class="collapse navbar-collapse" id="navbarNav">
  <ul class="navbar-nav">
    <li class="nav-item">
      <a class="nav-link" href="{% url 'projects' %}">Проекты</a>
    </li>
    <li class="nav-item">
      <a class="nav-link" href="{% url 'performers' %}">Исполнители</a>
    </li>
...
```


#### **12. Также создадим страницу для вывода всех задач**
В файле `taskmanager/templates/tasks/tasks.html`
```html
{% extends 'base.html' %}

{% block content %}
  <h1>Список задач</h1>
  <p class="mb-5 text-body-secondary">Все задачи в системе</p>
  <div class="row flex-column gap-4 align-items-center">
    {% for task in tasks %}
      <div class="col-12">
        <div class="card">
          <div class="card-body">
            <div class="d-flex align-items-center gap-2">
              <h5 class="card-title">{{ task.title }}</h5>
              <span class="badge text-bg-dark mb-1">{{ task.project.name }}</span>
            </div>
            <h6 class="card-subtitle mb-2 text-body-secondary">{{ task.status }} - {{ task.due_date }}</h6>
            <pre class="card-text">{{ task.description }}</pre>
            <a href="#" class="card-link">Посмотреть задачу</a>
          </div>
        </div>
      </div>
    {% empty %}
      <div class="col-12">
        <div class="card">
          <div class="card-body">
            <h5 class="card-title">Еще нет задач</h5>
            <h6 class="card-subtitle mb-2 text-body-secondary">Смотрю вы любите отдыхать...</h6>
            <p class="card-text">Всем известно, что слона проще есть по частям</p>
            <a href="#" class="card-link">Создать задачу</a>
          </div>
        </div>
      </div>
    {% endfor %}
  </div>
{% endblock %}
```

В файле `taskmanager/tasks/urls.py` добавим
```python
urlpatterns = [
  ...
  path('tasks/', views.tasks, name='tasks'),
]
```

В файле `taskmanager/tasks/views.py` добавим
```python
...

def tasks(request):
  tasks_list = Task.objects.all()
  return render(request, 'tasks/tasks.html', context={'tasks': tasks_list})
```

Незабываем изменить ссылки, во всех местах, где мы хотим ссылаться на страницу всех проектов.

В файле `taskmanager/includes/header.html` изменим
```html
...
    <li class="nav-item">
      <a class="nav-link" href="{% url 'performers' %}">Исполнители</a>
    </li>
    <li class="nav-item">
      <a class="nav-link" href="{% url 'tasks' %}">Задачи</a>
    </li>
  </ul>
</div>
...
```


#### **13. Создадим страницу подробного просмотра проекта**
В файле `taskmanager/templates/tasks/project.html`
```html
{% extends 'base.html' %}

{% block content %}
  <h1>{{ project.name }}</h1>
  <pre class="text-body-secondary">{{ project.description }}</pre>
  <p class="mb-5 text-body-secondary">Проект создан {{ project.created_at }}</p>

  <div class="row gap-3">
    <h2>Задачи проекта</h2>
    {% for task in tasks %}
      <div class="col-12">
        <div class="card">
          <div class="card-body">
            <h5 class="card-title">{{ task.title }}</h5>
            <h6 class="card-subtitle mb-2 text-body-secondary">{{ task.status }} - {{ task.due_date }}</h6>
            <pre class="card-text">{{ task.description }}</pre>
            <a href="#" class="card-link">Посмотреть задачу</a>
          </div>
        </div>
      </div>
    {% empty %}
      <div class="col-12">
        <div class="card">
          <div class="card-body">
            <h5 class="card-title">Еще нет задач</h5>
            <h6 class="card-subtitle mb-2 text-body-secondary">Смотрю вы любите отдыхать...</h6>
            <p class="card-text">Всем известно, что слона проще есть по частям</p>
            <a href="#" class="card-link">Создать задачу</a>
          </div>
        </div>
      </div>
    {% endfor %}
  </div>
{% endblock %}
```

В файле `taskmanager/tasks/urls.py` добавим
```python
urlpatterns = [
  ...
  path('projects/', views.projects, name='projects'),
  path('project/<int:project_id>', views.project, name='project'),
  ...
]
```

В файле `taskmanager/tasks/views.py` добавим
```python
...

def project(request, project_id):
  project_view = Project.objects.get(pk=project_id)
  tasks_list = Task.objects.filter(project_id=project_id)
  return render(request, 'tasks/project.html', context={'project': project_view, 'tasks': tasks_list})
  
...
```

Незабываем изменить ссылки, во всех местах, где мы хотим ссылаться на подробную страницу проекта.

В файле `taskmanager/tasks/projects.html` изменим
```html
...
            <pre class="card-text">{{ project.description }}</pre>
            <a href="{% url 'project' project.id %}" class="card-link">Посмотреть проект</a>
          </div>
        </div>
    </div>
{% empty %}
...
```

На этом пока останавливаемся. В результате должна получиться система управления проектами, с возможностью 
просматривать каждую сущность созданную в моделях django. 