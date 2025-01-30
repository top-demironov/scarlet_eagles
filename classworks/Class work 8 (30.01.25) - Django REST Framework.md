# Теоретический материал: Неделя 6, Занятие 2 – Введение в Django REST Framework и создание API

---

## **Цель занятия**

1. Познакомиться с Django REST Framework (DRF).
2. Изучить основные компоненты DRF: сериализаторы, представления и маршруты.
3. Создать простой REST API для работы с данными.

---

## **1. Что такое Django REST Framework (DRF)?**

Django REST Framework — это мощный и гибкий инструмент для создания веб-API. С его помощью вы можете быстро разработать REST API, который позволяет взаимодействовать с данными через HTTP-запросы.

---

### **1.1 Преимущества DRF**

1. **Простота:**
   - DRF интегрируется с Django, сохраняя привычный стиль работы.
2. **Функциональность из коробки:**
   - Поддержка сериализации, фильтрации, пагинации и аутентификации.
3. **Стандарт REST:**
   - Использование стандартных методов HTTP (GET, POST, PUT, DELETE).
4. **Документация:**
   - Автоматически создаёт удобную интерфейс-документацию для API.

---

## **2. Основные компоненты DRF**

### **2.1 Сериализаторы**
Сериализаторы преобразуют данные моделей в JSON (или другой формат), который может быть отправлен по API, а также преобразуют входящие данные обратно в объекты.

Пример сериализатора:
```python
from rest_framework import serializers
from .models import Task

class TaskSerializer(serializers.ModelSerializer):
    class Meta:
        model = Task
        fields = ['id', 'title', 'description', 'status', 'due_date']
```

### **2.2 Представления**
Представления обрабатывают HTTP-запросы и возвращают ответы. В DRF есть два основных подхода:
1. **FBV (Function-Based Views)**: Представления на основе функций.
2. **CBV (Class-Based Views)**: Представления на основе классов.

#### **Пример представления с APIView**
```python
from rest_framework.views import APIView
from rest_framework.response import Response
from .models import Task
from .serializers import TaskSerializer

class TaskListView(APIView):
    def get(self, request):
        tasks = Task.objects.all()
        serializer = TaskSerializer(tasks, many=True)
        return Response(serializer.data)
```

#### **FBV (Function-Based View)**
Можно использовать функциональные представления, которые более компактны, но менее удобны для расширения.

```python
from rest_framework.decorators import api_view
from rest_framework.response import Response
from .models import Task
from .serializers import TaskSerializer

@api_view(['GET'])
def task_list(request):
    tasks = Task.objects.all()
    serializer = TaskSerializer(tasks, many=True)
    return Response(serializer.data)
```

#### **CBV (Class-Based View)**
Использование классового подхода позволяет разделить логику API на отдельные методы.

```python
from rest_framework.generics import ListAPIView
from .models import Task
from .serializers import TaskSerializer

class TaskListView(ListAPIView):
    queryset = Task.objects.all()
    serializer_class = TaskSerializer
```

Использование `ListAPIView` автоматически обрабатывает получение списка объектов (`GET /tasks/`).

#### **Когда использовать CBV или FBV?**
- FBV (функции) удобны для простых API, где нужно обработать только 1-2 метода.
- CBV (классы) лучше подходят для REST API с CRUD-операциями и сложной бизнес-логикой.


### **2.3 Маршруты (URLs)**
Маршруты в Django REST Framework (DRF) связывают запросы клиентов с соответствующими представлениями API. Они определяют, как API будет обрабатывать входящие HTTP-запросы.

#### **2.3.1. Простые маршруты (URLs)**
Самый простой способ зарегистрировать представление API — использовать `path()` в файле `urls.py`:

```python
from django.urls import path
from .views import TaskListView

urlpatterns = [
    path('tasks/', TaskListView.as_view(), name='task-list'),
]
```

Здесь `TaskListView.as_view()` обрабатывает запросы, отправленные на `/tasks/`.

#### **2.3.2. Использование include() для организации маршрутов**
Если у вас несколько эндпоинтов API, можно использовать include():

```python
from django.urls import path, include

urlpatterns = [
    path('api/', include('tasks.urls')),  # Все API-эндпоинты будут доступны через /api/
]
```

Теперь все URL из `tasks/urls.py` будут доступны по префиксу `/api/`.

#### **2.3.3. Использование ViewSet и Router**
Django REST Framework предоставляет `ViewSet` и `Router`, которые упрощают настройку API.

##### 2.3.3.1 Определение `ViewSet`
```python
from rest_framework.viewsets import ModelViewSet
from .models import Task
from .serializers import TaskSerializer

class TaskViewSet(ModelViewSet):
    queryset = Task.objects.all()
    serializer_class = TaskSerializer
```

##### 2.3.3.2 Настройка маршрутов с `DefaultRouter`
В файле urls.py зарегистрируйте `ViewSet` с помощью `DefaultRouter`:

```python
from rest_framework.routers import DefaultRouter
from .views import TaskViewSet

router = DefaultRouter()
router.register('tasks', TaskViewSet)  # /api/tasks/

urlpatterns = router.urls
```

Теперь API автоматически поддерживает следующие маршруты:

- `GET /tasks/` – Получить список задач
- `POST /tasks/` – Создать новую задачу
- `GET /tasks/{id}/` – Получить конкретную задачу
- `PUT /tasks/{id}/` – Обновить задачу
- `DELETE /tasks/{id}/` – Удалить задачу

#### 2.3.4. Когда использовать path() или router.register()
- `path()` - Когда нужно определить URL вручную
- `router.register()` - Когда используется `ViewSet`, и требуется автоматическая маршрутизация


### 3.2 Пример взаимодействия
Пример HTTP-запроса для получения списка задач (GET):

```vbnet
GET /api/tasks/ HTTP/1.1
Host: example.com
```

Ответ сервера:
```json
[
    {
        "id": 1,
        "title": "Задача 1",
        "description": "Описание задачи 1",
        "status": "Выполняется",
        "due_date": "2025-01-31"
    }
]
```


### **4. Подключение DRF в проект**

Django REST Framework (DRF) не включён в стандартный пакет Django, поэтому его нужно установить и настроить перед использованием.

---

### **4.1 Установка DRF**
Установите Django REST Framework с помощью pip:
```bash
pip install djangorestframework
```

Затем добавьте `rest_framework` в `INSTALLED_APPS` в файле `settings.py`:
```pytnon
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework',  # Подключение DRF
]
```

После этого перезапустите сервер Django.

### **4.2 Базовые настройки DRF**
Вы можете настроить DRF через параметр `REST_FRAMEWORK` в `settings.py`:
```python
REST_FRAMEWORK = {
    'DEFAULT_RENDERER_CLASSES': [
        'rest_framework.renderers.JSONRenderer',  # Возвращает JSON-ответы
    ],
    'DEFAULT_PARSER_CLASSES': [
        'rest_framework.parsers.JSONParser',  # Парсит JSON-запросы
    ],
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.SessionAuthentication',
        'rest_framework.authentication.BasicAuthentication',
    ],
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',  # Доступ только для авторизованных пользователей
    ],
}
```

Эти настройки означают, что:

- Все API-запросы будут возвращаться в формате JSON.
- Поддерживается аутентификация через сессии Django и базовую HTTP-аутентификацию.
- Доступ к API открыт только для авторизованных пользователей.

---

## **5. Практическая работа**

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

