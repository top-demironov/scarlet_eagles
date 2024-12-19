# **Практика: Реализация регистрации, аутентификации и авторизации в Django**

## **Цель задания**

1. Научиться использовать встроенные механизмы Django для работы с пользователями.
2. Реализовать страницы регистрации, входа и выхода пользователей.
3. Ограничить доступ к определённым страницам через авторизацию.

## **Описание задания**

Добавьте в систему возможность управления пользователями, а именно:

1. Пользователи могут регистрироваться.
2. Пользователи могут входить и выходить из системы.
3. Определённые страницы доступны только авторизованным пользователям.

---

## **Этапы выполнения**

### **Реализация регистрации**

Из-за промежуточной таблицы, которая создается автоматически из-за связи многие-ко-многим могут возникнуть проблемы 
целостности, поэтому на данном этапе легче справиться с этими проблемами удалив базу данных. **Удаляем базу данных**. 

Мы уже создавали модель `User` в этом проекте. Это участники проектов, сейчас мы сделаем чтобы зарегистрированные 
пользователи могли стать участниками проектов. Для этого изменим нашу модель `User`. Унаследуем нашу модель от 
`AbstractUser`, это встроенная в джанго модель пользователя, уже содержит в себя нужные поля, а именно логин, пароль, 
почта и другие.

В файле `tasks/models.py` изменим:

```python
class User(AbstractUser):
  firstname = models.CharField(max_length=100)
  lastname = models.CharField(max_length=100)

  def __str__(self):
    return self.username
```

Создадим и выполним миграции.

```commandline
python manage.py makemigrations
python manage.py migrate
```

Перенастроим базовую модель аутентификации джанго на только что измененную нашу модель в файле `settings.py`:
```python
...

AUTH_PASSWORD_VALIDATORS = [
    {
        'NAME': 'django.contrib.auth.password_validation.UserAttributeSimilarityValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator',
    },
]


AUTH_USER_MODEL = 'tasks.User'  # Изменяем базовую модель на нашу

...
```

Создадим форму регистрации в файле `tasks/forms.py`

```python
...

class SignupForm(forms.ModelForm):
  confirm_password = forms.CharField(widget=forms.PasswordInput(attrs={
    'class': 'form-control',
    'id': 'floatingPasswordConfirm',
    'placeholder': 'Подтверждение пароля',
    'required': True
  }))

  class Meta:
    model = User
    fields = ['firstname', 'lastname', 'username', 'email', 'password']

    widgets = {
      'firstname': forms.TextInput(attrs={
        'class': 'form-control',
        'id': 'floatingTitle',
        'placeholder': 'Очень простая задача',
        'required': True
      }),
      'lastname': forms.TextInput(attrs={
        'class': 'form-control',
        'id': 'floatingLastname',
        'aria-label': 'Фамилия',
        'placeholder': 'Очень простая задача',
        'required': True
      }),
      'username': forms.TextInput(attrs={
        'class': 'form-control',
        'id': 'floatingUsername',
        'placeholder': 'Логин пользователя',
        'aria-label': 'Логин пользователя',
        'required': True
      }),
      'email': forms.EmailInput(attrs={
        'class': 'form-control',
        'id': 'floatingEmail',
        'placeholder': 'Почта',
        'required': True
      }),
      'password': forms.PasswordInput(attrs={
        'class': 'form-control',
        'id': 'floatingPassword',
        'placeholder': 'Пароль',
        'required': True
      }),
    }

  def clean(self):
    cleaned_data = super().clean()
    password = cleaned_data.get('password')
    confirm_password = cleaned_data.get('confirm_password')
    if password != confirm_password:
      raise forms.ValidationError('Пароли не совпадают!')
    return cleaned_data
```

Создадим файл `templates/auth/signup.html`:

```html
{% extends 'base.html' %}

{% block content %}
    <h1 class="mb-5">Регистрация</h1>
    <form action="{% url 'signup' %}" method="post">
        {% csrf_token %}
        <div class="form-floating mb-3">
            {{ form.firstname }}
            <label for="floatingFirstname">Имя</label>
        </div>
        <div class="form-floating mb-3">
            {{ form.lastname }}
            <label for="floatingLastname">Фамилия</label>
        </div>
        <div class="form-floating mb-3">
            {{ form.username }}
            <label for="floatingLastname">Логин</label>
        </div>
        <div class="form-floating mb-3">
            {{ form.email }}
            <label for="floatingEmail">Почта</label>
        </div>
        <div class="form-floating mb-3">
            {{ form.password }}
            <label for="floatingPassword">Пароль</label>
        </div>
        <div class="form-floating mb-3">
            {{ form.confirm_password }}
            <label for="floatingPasswordConfirm">Подтверждение пароля</label>
        </div>
        <div class="d-flex pt-4">
            <button type="submit" class="btn btn-primary">Создать задачу</button>
        </div>
    </form>
{% endblock %}
```

В файле `tasks/views.py` добавим:
```python
def signup_view(request):
  if request.method == 'POST':
    form = SignupForm(request.POST)
    if form.is_valid():
      user = form.save(commit=False)
      user.set_password(form.cleaned_data['password'])  # Хэшируем пароль
      user.save()
      return redirect('signin')
  else:
    form = SignupForm()
  return render(request, 'auth/signup.html', {'form': form})
```

В файле `tasks/urls.py` добавляем:

```python
...
  path('signup', views.signup, name='signup'),
]
```

На данный момент после успешной регистрации происходит редирект на страницу `login`, которой у нас еще нет, 
поэтому нормально если будет возникать ошибка.

На данном моменте появляется возможность регистрации пользователей (или исполнителей).


### **Реализация входа в систему**

В файле `tasks/views.py` добавим:

```python
...

def signin(request):
  if request.method == "POST":
    username = request.POST.get("username")
    password = request.POST.get("password")
    
    # Аутентификация пользователя
    user = authenticate(request, username=username, password=password)
    if user is not None:
      login(request, user)  # Вход пользователя
      return redirect('index')  # Перенаправление после успешного входа
    else:
      # Ошибка входа
      return render(request, 'auth/signin.html', {'error': 'Неверный логин или пароль'})
  return render(request, 'auth/signin.html')
```

Создадим файл `templates/auth/singin.html`:

```html
{% extends 'base.html' %}

{% block content %}
    <h1 class="mb-5">Вход в систему</h1>
    <form method="post">
        {% csrf_token %}
        <div class="form-group mb-3">
            <label for="username">Имя пользователя:</label>
            <input type="text" name="username" id="username" class="form-control" required>
        </div>
        <div class="form-group mb-3">
            <label for="password">Пароль:</label>
            <input type="password" name="password" id="password" class="form-control" required>
        </div>
         {% if error %}
            <p style="color: red;">{{ error }}</p>
        {% endif %}
        <div class="d-flex justify-content-between">
            <button type="submit" class="btn btn-primary">Войти</button>
            <a href="{% url 'signup' %}" class="btn btn-outline-secondary">Зарегистрироваться</a>
        </div>
    </form>
{% endblock %}
```

В файле `tasks/urls.py` изменим:

```python
...
  path('signin', views.signin, name='signin'),
]
```

### **Реализация выхода из системы**

В файле `tasks/views.py` добавим:

```python
...

def signout(request):
  # Выходим из системы
  logout(request)
  # Перенаправляем пользователя на страницу входа
  return redirect('signin')
```

В файле `tasks/urls.py` добавим:

```python
...

  path('signout/', views.signout, name='signout'),
]
```

### **Произведем изменения в html**

Добавим в нашем `header` возмоность отображения какой пользователь авторизован, а если не авторизован, то кнопку войти.

```html
...

    </ul>
    <div class="ms-auto">
        {% if user.is_authenticated %}
            <span class="me-3">{{ user.lastname }} {{ user.firstname }}</span>
            <a href="{% url 'signout' %}" class="btn btn-dark">Выйти</a>
        {% else %}
            <a href="{% url 'signin' %}" class="btn btn-dark">Войти</a>
        {% endif %}
    </div>
</div>

...
```

### **Ограничение доступа**

В файле `tasks/views.py` добавим декоратор `@login_reqiured` у всех функций view, которые нуждаются в ограничении доступа:

```python
@login_required
def projects(request):
  projects_list = Project.objects.all()
  return render(request, 'tasks/project/list.html', context={'projects': projects_list})
```

Также сделаем для функций `project`, `create_project`, `performers`, `tasks`, `task`, `create_task`, `signout`.

Добавим ссылочные редиректы в файле `settings.py`:

```python
...
AUTH_USER_MODEL = 'tasks.User'

LOGIN_URL = '/signin/'  #  Автоматический редирект на эту страницу, если для отображения страницы нужна авторизация
LOGIN_REDIRECT_URL = '/index/'  #  Автоматический редирект, когда пользователь произвел аутентификацию
LOGOUT_REDIRECT_URL = '/index/'  #  Автоматический редирект, если пользователь произвел выход из системы
...
```

## Итог
После выполнения задания вы:

1. Создадите систему регистрации и входа для пользователей.
2. Настроите доступ к страницам через авторизацию.
3. Реаизуете пользовательские страницы входа и выхода.

Если возникнут вопросы, обсудим их на следующем занятии!

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
