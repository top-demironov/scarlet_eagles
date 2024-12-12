# **Практика: Разработка системы задач и прогресса (Работа с формами)**

## **Цель задания**

1. Закрепить знания о формах в Django.
2. Научиться использовать "ручной" метод и Django Forms.
3. Дополнить систему управления задачами с отслеживанием их статуса и прогресса.

## **Описание задания**

1. Изменить методику хранения статуса задачи (новая, в процессе, завершена).
2. Добавить возможность управление проектом (добавить).
3. Добавить возможность управление задачами (добавить, просмотреть, изменить).

---

## **Этапы выполнения**

### **Изменим методику хранения статуса задачи**

Создадим еще одну модель `TaskStatus`, в ней будут храниться состояния задачи.

В файле `taskmanager/tasks/models.py` добавим
```python
...
class TaskStatus(models.Model):
  name = models.CharField(max_length=100)
  description = models.TextField()

  def __str__(self):
    return self.name


class Task(models.Model):
  title = models.CharField(max_length=100)
...
```

В файле `taskmanager/tasks/models.py` изменим тип поля `status`, укажем в поле `on_delete` значение `DO_NOTHING`

```python
...
class Task(models.Model):
  title = models.CharField(max_length=100)
  description = models.TextField()
  status = models.ForeignKey(TaskStatus, on_delete=models.DO_NOTHING)
  created_at = models.DateTimeField(auto_now_add=True)
...
```

В файле `taskmanager/tasks/admin.py` зарегистрируем модель в админ-панели 
```python
...
admin.site.register(User)
admin.site.register(TaskStatus)
```

Перед выполнением следующих команд удалим ранее созданные проекты и задачи, чтобы не произошло конфликтов при миграциях. Используйте для этого админ-панель, python shell или встроенную СУБД Pycharm.

После выполним следующие команды для создания файла миграций и изменения структуры базы данных:
```commandline
python manage.py makemigrations
python manage.py migrate
```

Создадим статусы задач с помощью админ-панели. В дальнейшем, такая структура, позволит нам добавлять новые статусы.


### **Добавим возможность создавать проект**
 
Изменим названия файлов и их нахождения в каталоге `templates` для лучшего восприятия структуры. В скобочках указано прошлое название файла.
Не забудьте изменить пути в файле `tasks/views.py`
```text
──templates
  └───project
      ├───details.html (бывший project.html)
      ├───create.html (новый файл)
      └───list.html (бывший projects.html)
```

В файле `templates/project/create.html`
```html
{% extends 'base.html' %}

{% block content %}
    <h1 class="mb-5">Создание проекта</h1>
    <form action="{% url 'projects_create' %}" method="post">
        {% csrf_token %}
        <div class="form-floating mb-3">
          <input name="name" type="text" class="form-control" id="floatingName" placeholder="Самый лучший проект">
          <label for="floatingName">Название проекта</label>
        </div>
        <div class="form-floating">
          <textarea name="description" class="form-control" id="floatingDescription" placeholder="Описание проекта" style="height: 160px;"></textarea>
          <label for="floatingDescription">Описание</label>
        </div>
        <div class="d-flex pt-4">
            <button type="submit" class="btn btn-primary">Создать проект</button>
        </div>
    </form>
{% endblock %}
```


В файле `tasks/views.py` добавим
```python
...
def create_project(request):
  if request.method == 'POST':
    name = request.POST['name']
    description = request.POST['description']
    project_view = Project.objects.create(name=name, description=description)
    return redirect('project', project_view.id)
  return render(request, 'tasks/project/create.html')
...
```

В файле `tasks/urls.py` добавим
```python
...
  path('projects/', views.projects, name='projects'),
  path('projects/create/', views.create_project, name='projects_create'),
  path('project/<int:project_id>/', views.project, name='project'),
...
```

Незабываем произвести замену ссылок в html-верстке на:
```html
{% url 'projects_create' %}
```

### **Добавим возможность создавать задачу**

Изменим названия файлов и их нахождения в каталоге `templates` для лучшего восприятия структуры. В скобочках указано прошлое название файла.
Не забудьте изменить пути в файле `tasks/views.py`
```text
──templates
  └───task
      ├───details.html (новый файл)
      ├───create.html (новый файл)
      └───list.html (бывший tasks.html)
```

Создадим файл `tasks/forms.py`
```python
from django import forms
from .models import Task

class TaskCreateForm(forms.ModelForm):
  class Meta:
    model = Task
    fields = ['project', 'title', 'description', 'due_date']
  
    widgets = {
      'project': forms.Select(attrs={
        'class': 'form-select',
        'id': 'floatingProjectSelect',
        'aria-label': 'Выбор проекта для привязки задачи',
        'required': True
      }),
      'title': forms.TextInput(attrs={
        'class': 'form-control',
        'id': 'floatingTitle',
        'placeholder': 'Очень простая задача',
        'required': True
      }),
      'description': forms.TextInput(attrs={
        'class': 'form-control',
        'id': 'floatingDescription',
        'placeholder': 'Нужно сделать несколько пунктов...',
        'required': True
      }),
      'due_date': forms.DateInput(attrs={
        'class': 'form-control',
        'id': 'floatingDueDate',
        'type': 'date',
        'required': True
      }),
    }
```

В файле `templates/task/create.html`
```html
{% extends 'base.html' %}

{% block content %}
    <h1 class="mb-5">Создание задачи</h1>
    
    <form action="{% url 'tasks_create' %}" method="post">
        {% csrf_token %}
    
        <div class="form-floating mb-3">
            {{ form.project }}
            <label for="floatingSelect">Проект</label>
        </div>
        <div class="form-floating mb-3">
            {{ form.title }}
            <label for="floatingTitle">Название задачи</label>
        </div>
        <div class="form-floating mb-3">
            {{ form.description }}
            <label for="floatingDescription">Описание задачи</label>
        </div>
        <div class="form-floating mb-3">
            {{ form.due_date }}
            <label for="floatingDueDate">Срок исполнения задачи</label>
        </div>
        <div class="d-flex pt-4">
            <button type="submit" class="btn btn-primary">Создать задачу</button>
        </div>
    </form>
{% endblock %}
```

В файле `tasks/urls.py`
```python
...
  path('tasks/', views.tasks, name='tasks'),
  path('tasks/create/', views.create_task, name='tasks_create'),
]
```

В файле `tasks/views.py`
```python
def create_task(request):
  if request.method == "POST":
    form = TaskCreateForm(request.POST)
    if form.is_valid():
      # form.save()  # В случае если не нужно изменять сущность задачи
      task = form.save(commit=False)
      task.status = TaskStatus.objects.get(name="Новая")
      task.save()
      return redirect('tasks')
  else:
    form = TaskCreateForm()
  
  projects_list = Project.objects.all()
  return render(request, 'tasks/task/create.html', context={'form': form, 'projects': projects_list})
```

Незабываем произвести замену ссылок в html-верстке.


### **Добавим возможность просматривать и изменять задачу**

В файле `tasks/forms.py` добавим
```python
...
class TaskForm(forms.ModelForm):
	class Meta:
		model = Task
		fields = ['project', 'title', 'description', 'due_date', 'status']  # Все поля, которые можно редактировать
		widgets = {
			'project': forms.Select(attrs={'class': 'form-select'}),
			'title': forms.TextInput(attrs={'class': 'form-control'}),
			'description': forms.Textarea(attrs={'class': 'form-control'}),
			'due_date': forms.DateInput(attrs={'class': 'form-control', 'type': 'date'}),
			'status': forms.Select(attrs={'class': 'form-select'}),
		}
```

В файле `templates/task/details.html`
```html
{% extends 'base.html' %}

{% block content %}
    <h1 class="mb-5">Редактирование задачи: {{ task.title }}</h1>

    <form action="" method="post">
        {% csrf_token %}
        <div class="form-floating mb-3">
            {{ form.project }}
            {{ form.project.label_tag }}
        </div>
        <div class="form-floating mb-3">
            {{ form.title }}
            {{ form.title.label_tag }}
        </div>
        <div class="form-floating mb-3">
            {{ form.description }}
            {{ form.description.label_tag }}
        </div>
        <div class="form-floating mb-3">
            {{ form.due_date }}
            {{ form.due_date.label_tag }}
        </div>
        <div class="form-floating mb-3">
            {{ form.status }}
            {{ form.status.label_tag }}
        </div>

        <button type="submit" class="btn btn-primary">Сохранить изменения</button>
        <a href="{% url 'tasks' %}" class="btn btn-secondary">Отмена</a>
    </form>
{% endblock %}
```

В файле `tasks/urls.py`
```python
...
  path('tasks/', views.tasks, name='tasks'),
  path('tasks/create/', views.create_task, name='tasks_create'),
  path('tasks/<int:task_id>/', views.task, name='task'),
]
```

В файле `tasks/views.py`
```python
def task(request, task_id):
  task_view = Task.objects.get(pk=task_id)  # Получаем объект Task по первичному ключу
  if request.method == "POST":
    form = TaskForm(request.POST, instance=task_view)  # Передаём существующий объект в форму
    if form.is_valid():
      form.save()
      return redirect('tasks')
  else:
    form = TaskForm(instance=task_view)  # Предзаполняем форму текущими данными
  return render(request, 'tasks/task/details.html', {'form': form, 'task': task})
```

Незабываем произвести замену ссылок в html-верстке.

---

### После выполнения работы переходите к выполнению домашнего задания

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
