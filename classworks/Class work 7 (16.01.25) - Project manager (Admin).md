# Классная работа – Настройка административной панели Django

---

## Цель занятия

1. Освоить базовую настройку административной панели Django.
2. Изучить методы кастомизации моделей в админке.
3. Настроить удобный интерфейс для управления данными через административную панель.

---


## **1. Теория: Административная панель Django**

### **1.1 Зачем нужна админка?**
Административная панель предоставляет готовый интерфейс для управления данными, определёнными в моделях. Она позволяет:

- Добавлять, редактировать и удалять записи.
- Удобно просматривать и фильтровать данные.
- Управлять пользователями, группами и правами доступа.

---

## **2. Пример работы с административной панелью Django**

### **Шаг 1: Локализация админки**
Для перевода административной панели на русский язык измените настройки в `settings.py`:
```python
LANGUAGE_CODE = 'ru'  # Установите русский язык
TIME_ZONE = 'Asia/Krasnoyarsk'  # Укажите ваш часовой пояс
USE_L10N = True  # Локализация форматов даты/времени
USE_TZ = True  # Использование часовых поясов
```

### **Шаг 2: Изменение заголовков**
Для изменения заголовков и текста на главной странице админки пропищите следующее в файле `tasks/admin.py`:
```python
from django.contrib import admin

admin.site.site_header = "Управление проектами"
admin.site.site_title = "Админка Task Manager"
admin.site.index_title = "Добро пожаловать в Task Manager"
```

### **Шаг 3: Настройка отображения моделей**
Админка позволяет настроить, как ваши модели будут отображаться. Для этого используется класс `ModelAdmin`.

В файле `tasks/admin.py` добавим:
```python
from django.contrib import admin
from django.contrib.auth.admin import UserAdmin

from tasks.models import Project, Task, User, TaskStatus

...

...

@admin.register(Task)
class TaskAdmin(admin.ModelAdmin):
  list_display = ('title', 'status', 'due_date', 'project')  # Поля для отображения в списке
  list_filter = ('status', 'project')  # Фильтры в правой части админки
  search_fields = ('title', 'description')  # Поля для поиска
  ordering = ('due_date',)  # Сортировка записей


@admin.register(Project)
class ProjectAdmin(admin.ModelAdmin):
  list_display = ('name', 'description', 'created_at')  # Поля для отображения в списке


@admin.register(User)
class UserAdmin(UserAdmin):
  list_display = ('username', 'email', 'firstname', 'lastname', 'is_staff')
  fieldsets = UserAdmin.fieldsets + (
    ('Дополнительная информация', {'fields': ('firstname', 'lastname')}),
  )


@admin.register(TaskStatus)
class TaskStatusAdmin(admin.ModelAdmin):
  list_display = ('name', 'description')  # Поля для отображения в списке
```

### **Шаг 4: Изменение названия колонок моделей**
Для полной локализации также нужно добавить `verbose_name` и `verbose_name_plural` в моделях.

Изменим `tasks/models.py`:

```python
from django.contrib.auth.models import AbstractUser
from django.db import models


class Project(models.Model):
  class Meta:
    verbose_name = 'проект'
    verbose_name_plural = 'проекты'

  name = models.CharField(max_length=100, verbose_name='Название')
  description = models.TextField(verbose_name='Описание')
  created_at = models.DateTimeField(auto_now_add=True, verbose_name='Создан')

  def __str__(self):
    return self.name


class User(AbstractUser):
  class Meta:
    verbose_name = 'пользователь'
    verbose_name_plural = 'пользователи'

  firstname = models.CharField(max_length=100, verbose_name='Имя')
  lastname = models.CharField(max_length=100, verbose_name='Фамилия')

  def __str__(self):
    return self.username


class TaskStatus(models.Model):
  class Meta:
    verbose_name = 'статус задачи'
    verbose_name_plural = 'статусы задачи'
  name = models.CharField(max_length=100, verbose_name='Название')
  description = models.TextField(verbose_name='Описание')

  def __str__(self):
    return self.name


class Task(models.Model):
  class Meta:
    verbose_name = 'задача'
    verbose_name_plural = 'задачи'

  title = models.CharField(max_length=100, verbose_name='Название')
  description = models.TextField(verbose_name='Описание')
  status = models.ForeignKey(TaskStatus, on_delete=models.PROTECT, verbose_name='Статус')
  created_at = models.DateTimeField(auto_now_add=True, verbose_name='Создана')
  due_date = models.DateField(verbose_name='Срок выполнения')
  project = models.ForeignKey(Project, on_delete=models.CASCADE, verbose_name='Проект')
  performers = models.ManyToManyField(User, verbose_name='Исполнители')

  def __str__(self):
    return f'{self.title} ({self.status})'
```

Также для изменения названия группы, нам нужно добавить `verbose_name` у нашего приложения.

В файле `tasks/apps.py` добавим:
```python
class TasksConfig(AppConfig):
  default_auto_field = 'django.db.models.BigAutoField'
  name = 'tasks'
  verbose_name = 'Задачи и проекты'  # Название группы в админке
```

---

## **3. Практическая работа**

Выполняем практическую работу.

---

<div style="display: flex; padding-bottom: 40px; gap: 10px;">
  <a style="
    display: block;
    text-decoration: none;
    color: white;
    padding: 10px;
    border-radius: 10px;
    background: #0d6efd;" href="https://forms.gle/224CibgUdUJPbi6u9">Мне понравилось</a>
  <a style="
    display: block;
    text-decoration: none;
    color: white;
    padding: 10px;
    border-radius: 10px;
    background: #6c757d;" href="https://forms.gle/224CibgUdUJPbi6u9">Мне не понравилось</a>
</div>

