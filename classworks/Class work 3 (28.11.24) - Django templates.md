# Классная работа – Шаблоны и статика в Django (С глобальными шаблонами, хедером и футером)

---

## Цель занятия

1. Научиться работать с шаблонами Django.
2. Изучить подключение CSS, JS и изображений.
3. Организовать структуру шаблонов с использованием отдельных файлов для хедера и футера.

---

## Содержание

1. Что такое шаблоны в Django?
2. Создание структуры шаблонов
3. Работа со статическими файлами
4. Подключение CSS и JS

---

## 1. Что такое шаблоны в Django?

Шаблоны в Django используются для создания HTML-страниц с динамическим содержимым. Они позволяют:

- Вставлять переменные из представлений (`views`) в HTML.
- Использовать теги и фильтры для обработки данных (например, циклы, условия).
- Организовывать многоуровневые шаблоны с использованием базовых шаблонов.

---

## 2. Создание структуры шаблонов

### 1. Расположение папки `templates`

Шаблоны будут находиться на уровне файла `manage.py`:

```text
project_name/
    manage.py
    templates/
        base.html
        partials/
            header.html
            footer.html
        blog/
            index.html
```

### 2. Создание хедера (`partials/header.html`):
```html
<header>
    <h1>My Blog</h1>
    <nav>
        <ul>
            <li><a href="/">Главная</a></li>
            <li><a href="/about/">О нас</a></li>
            <li><a href="/contact/">Контакты</a></li>
        </ul>
    </nav>
</header>
```

### 3. Создание футера (`partials/footer.html`):
```html
<footer>
    <p>© 2024 My Blog</p>
</footer>
```

### 4. Создание базового шаблона (`base.html`):
```html
<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}My Blog{% endblock %}</title>
    <link rel="stylesheet" href="{% static 'css/style.css' %}">
</head>
<body>
    {% include 'partials/header.html' %}
    <main>
        {% block content %}{% endblock %}
    </main>
    {% include 'partials/footer.html' %}
</body>
</html>
```

### 5. Пример шаблона страницы (`blog/index.html`):
```html
{% extends 'base.html' %}

{% block title %}Главная{% endblock %}

{% block content %}
    <h2>Добро пожаловать в мой блог!</h2>
    <p>Здесь вы найдете последние публикации.</p>
{% endblock %}
```

---

## 3. Работа со статическими файлами

### 1. Расположение папки `static`:
Статические файлы также должны находиться на уровне файла manage.py:
```text
project_name/
    manage.py
    static/
        css/
            style.css
        js/
            script.js
        images/
            logo.png
```

### 2. Настройка `settings.py`:
Добавьте путь к статическим файлам:

```python
STATIC_URL = '/static/'
STATICFILES_DIRS = [os.path.join(BASE_DIR, 'static')]
```

### 3. Подключение статических файлов в шаблонах:
Загрузите статику с помощью тега {% load static %}:

```html
<link rel="stylesheet" href="{% static 'css/style.css' %}">
```

## 4. Подключение CSS и JS

### 1. CSS (пример `style.css`):

```css
body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
}

header {
    background-color: #333;
    color: white;
    padding: 10px 20px;
    text-align: center;
}

header nav ul {
    list-style-type: none;
    margin: 0;
    padding: 0;
    display: flex;
    justify-content: center;
}

header nav ul li {
    margin: 0 15px;
}

header nav ul li a {
    color: white;
    text-decoration: none;
}

footer {
    background-color: #333;
    color: white;
    text-align: center;
    padding: 10px 20px;
    position: fixed;
    bottom: 0;
    width: 100%;
}
```

### 2. JavaScript (пример `script.js`):

```javascript
document.addEventListener('DOMContentLoaded', () => {
    console.log('Страница загружена');
});
```

---

## Итоги
После изучения материала вы должны уметь:

1. Работать с шаблонами Django, размещенными в глобальной папке.
2. Создавать отдельные файлы для хедера и футера и подключать их.
3. Подключать и использовать глобальные статические файлы.
4. Создавать базовые и расширенные шаблоны.