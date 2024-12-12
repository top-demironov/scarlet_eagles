# Классная работа – Работа с формами в Django

---

## Цель занятия

1. Изучить работу с формами в Django.
2. Изучить стандартные формы и их валидацию.
3. Реализовать обработку данных, отправленных пользователем через форму.

---


## **1. Что такое формы в Django?**

Форма в Django — это способ взаимодействия с пользователем, который позволяет:

- Получать и обрабатывать данные от пользователей.
- Валидировать данные.
- Генерировать HTML-код формы на основе Python-кода.

Django предоставляет два способа работы с формами:

1. **Классический метод (без использования Django Forms):** Вы самостоятельно пишете HTML-код формы и обрабатываете данные через представления.
2. **Использование встроенного функционала Django Forms:** Автоматизация обработки данных, валидации и рендеринга формы.
---

## **2. Работа с формами "ручным" методом**

**Создание HTML-формы.** Вы вручную пишете HTML для формы:

```html
<form method="post" action="/submit/">
  <label for="username">Имя пользователя:</label>
  <input type="text" name="username" id="username" required>
  <label for="email">Email:</label>
  <input type="email" name="email" id="email" required>
  <button type="submit">Отправить</button>
</form>
```

**Обработка данных в представлении.** В Django представление обрабатывает данные, отправленные пользователем:

```python
from django.shortcuts import render
from django.http import HttpResponse

def submit_form(request):
  if request.method == "POST":
    username = request.POST.get("username")
    email = request.POST.get("email")
    # Валидация данных (например, проверка email)
    if not username or not email:
      return HttpResponse("Ошибка: Все поля обязательны для заполнения.")
    return HttpResponse(f"Данные получены: {username}, {email}")
  return render(request, "form.html")
```

**Особенности:**
- Полный контроль над HTML и обработкой данных.
- Валидацию данных вы реализуете вручную.
- Нет автоматизации.
---

## **3. Работа с Django Forms**

**Создание формы с использованием Django Forms.** Django предоставляет специальный класс для работы с формами — forms.Form:

```python
from django import forms

class ContactForm(forms.Form):
  username = forms.CharField(max_length=100, label="Имя пользователя")
  email = forms.EmailField(label="Email")
```

**Рендеринг формы в шаблоне.** В представлении создайте экземпляр формы и передайте его в шаблон:

```python
from django.shortcuts import render

def contact_view(request):
  form = ContactForm()
  return render(request, "contact.html", {"form": form})
```

В шаблоне отобразите форму:

```html
<form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Отправить</button>
</form>
```

**Обработка данных формы.** Django автоматически валидирует данные, если вы используете `is_valid()`:

```python
def contact_view(request):
  if request.method == "POST":
    form = ContactForm(request.POST)
    if form.is_valid():
      username = form.cleaned_data["username"]
      email = form.cleaned_data["email"]
      return HttpResponse(f"Данные получены: {username}, {email}")
  else:
    form = ContactForm()
  return render(request, "contact.html", {"form": form})
```

**Валидация данных.** Django Forms автоматически валидирует данные на основе типа поля. Например:

- CharField проверяет длину строки.
- EmailField проверяет правильность формата email.

---

## 4. Сравнения двух подходов

| **Критерий**               | **Ручной метод**                | **Django Forms**                    |
|----------------------------|---------------------------------|-------------------------------------|
| **Простота использования** | Требует больше ручного кода.    | Упрощает создание и обработку форм. |
| **Автоматизация**          | В ся валидация пишется вручную. | Встроенная валидация данных.        |
| **Гибкость**               | Полный контроль над HTML.       | Генерация HTML через Python-код.    |
| **Скорость разработки**    | Требует больше времени.         | Быстрое создание форм.              |

**Рекомендации по выбору подхода:**

- Используйте Django Forms, если вам нужно быстро создать форму с минимальными усилиями.
- Выбирайте ручной метод, если требуется полный контроль над структурой HTML или если вы хотите использовать кастомные решения для валидации и обработки данных.

---

## 5. Выполните практическую

Выполните практическую работу для закрепления материала.

---

<style>
  .buttons {
    display: flex;
    padding-bottom: 40px;
    gap: 10px
  }

  .buttons a {
    display: block;
    text-decoration: none;
    color: white;
    padding: 10px;
    border-radius: 10px;
  }

  .buttons a.primary {
    background: #0d6efd;
  }

  .buttons a.secondary {
    background: #6c757d;
  }
</style>

<div class="buttons">
  <a class="primary" href="https://forms.gle/224CibgUdUJPbi6u9">Мне понравилось</a>
  <a class="secondary" href="https://forms.gle/224CibgUdUJPbi6u9">Мне не понравилось</a>
</div>

