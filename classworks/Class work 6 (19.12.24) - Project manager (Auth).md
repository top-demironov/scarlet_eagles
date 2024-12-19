# Классная работа – Аутентификация и авторизация в Django

---

## Цель занятия

1. Изучить механизмы аутентификации и авторизации в Django.
2. Научиться использовать встроенную систему пользователей.
3. Реализовать вход и выход пользователей на сайте.
4. Создать систему регистрации нового пользователя.

---


## **1. Теория: Аутентификация и авторизация в Django**

### **1.1 Аутентификация vs Авторизация**
- **Аутентификация** — процесс проверки личности пользователя (например, через логин и пароль).
- **Авторизация** — процесс проверки, есть ли у пользователя доступ к определённым ресурсам или действиям.

### **1.2 Встроенная система пользователей Django**
Django предоставляет готовую систему для работы с пользователями:
- **Модель пользователя (`User`)** — встроенная модель для хранения данных о пользователях.
- **Функции аутентификации:**
  - `login()` — для входа пользователя.
  - `logout()` — для выхода пользователя.
  - `authenticate()` — для проверки логина и пароля.

### **1.3 Основные URL и шаблоны**
Django включает готовые URL для аутентификации:
- `/login/` — для входа.
- `/logout/` — для выхода.
- `/password_change/` — изменение пароля.
- `/password_reset/` — восстановление пароля.

---

## **2. Пример работы с аутентификацией и авторизацией**

### **Шаг 1: Настройка приложения для аутентификации**
1. Включите приложение `django.contrib.auth` в `INSTALLED_APPS` (по умолчанию оно уже включено).
2. Убедитесь, что в вашем проекте настроено `TEMPLATES` для шаблонов.

### **Шаг 2: Реализация входа и выхода пользователя**

#### **2.1 Представление для входа**
Используйте встроенное представление `LoginView`:
```python
from django.contrib.auth.views import LoginView

class CustomLoginView(LoginView):
  template_name = 'auth/login.html'
```

Добавьте маршрут в `urls.py`:
```python
from django.urls import path
from .views import CustomLoginView

urlpatterns = [
  path('login/', CustomLoginView.as_view(), name='login'),
]
```

Шаблон `auth/login.html`:

```html
<h1>Вход</h1>
<form method="post" action="{% url 'login' %}">
  {% csrf_token %}
  {{ form.as_p }}
  <button type="submit">Войти</button>
</form>
```

#### **2.1 Представление для выхода**

Используйте встроенное представление `LogoutView`:
```python
from django.contrib.auth.views import LogoutView

urlpatterns += [
  path('logout/', LogoutView.as_view(next_page='/'), name='logout'),
]
```

Ссылка на выход:

```html
<a href="{% url 'logout' %}">Выйти</a>
```

### **Шаг 3: Создание системы регистрации**

#### **3.1 Форма регистрации**

Создайте форму в `forms.py`:

```python
from django import forms
from django.contrib.auth.models import User

class RegisterForm(forms.ModelForm):
  password = forms.CharField(widget=forms.PasswordInput)
  confirm_password = forms.CharField(widget=forms.PasswordInput)
    
  class Meta:
    model = User
    fields = ['username', 'email', 'password']
  
  def clean(self):
    cleaned_data = super().clean()
    password = cleaned_data.get('password')
    confirm_password = cleaned_data.get('confirm_password')
    if password != confirm_password:
      raise forms.ValidationError("Пароли не совпадают!")
    return cleaned_data
```

#### **3.2 Представление для регистрации**

Добавьте представление в `views.py`:

```python
from django.shortcuts import render, redirect
from .forms import RegisterForm
from django.contrib.auth.models import User

def register_view(request):
  if request.method == "POST":
    form = RegisterForm(request.POST)
    if form.is_valid():
      user = form.save(commit=False)
      user.set_password(form.cleaned_data['password'])  # Хэшируем пароль
      user.save()
      return redirect('login')
  else:
    form = RegisterForm()
  return render(request, 'auth/register.html', {'form': form})
```

Добавьте маршрут в `urls.py`:
```python
urlpatterns += [
    path('register/', register_view, name='register'),
]
```

Шаблон `auth/register.html`:
```html
<h1>Регистрация</h1>
<form method="post">
  {% csrf_token %}
  {{ form.as_p }}
  <button type="submit">Зарегистрироваться</button>
</form>
```

### **Шаг 4: Ограничение доступа к страницам**

Используйте декоратор `@login_required` для защиты представлений:
```python
from django.contrib.auth.decorators import login_required

@login_required
def dashboard_view(request):
  return render(request, 'dashboard.html')
```

Добавьте редирект на страницу входа в `settings.py`:
```python
LOGIN_URL = '/login/'
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

