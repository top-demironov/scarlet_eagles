
# Занятие: Работа с формами и CRUD-операциями в Django

---

## **1. Задание: Разработка системы управления товарами**

### **Цель занятия**
К концу занятия студенты должны:
1. Научиться работать с CRUD-операциями (создание, чтение, обновление, удаление данных) в Django.
2. Использовать Django-модели для работы с базой данных.
3. Разработать приложение для управления товарами с основным функционалом.

---

### **Описание задачи**
Разработать приложение для управления товарами. Приложение должно позволять:
1. Добавлять новые товары.
2. Просматривать список товаров.
3. Обновлять информацию о товарах.
4. Удалять товары.

---

### **Этапы выполнения**

#### **1. Создайте проект Django**
- Назовите проект `productmanager`.

#### **2. Создайте приложение**
- Внутри проекта создайте приложение `products`.

#### **3. Настройте приложение**
- Добавьте `products` в список `INSTALLED_APPS` в файле `settings.py`.

#### **4. Создайте модель данных**
- Опишите модель `Product` с полями:
  - `name` — название товара (строка, максимум 200 символов).
  - `description` — описание товара (текстовое поле).
  - `price` — цена товара (десятичное число с двумя знаками после запятой).
  - `created_at` — дата добавления товара.

#### **5. Примените миграции**
- Создайте и примените миграции для модели `Product`.

#### **6. Настройте маршруты**
- Создайте файл `products/urls.py` и настройте маршруты для:
  - Списка товаров (`''`).
  - Добавления нового товара (`'add/'`).
  - Редактирования товара (`'edit/<int:id>/'`).
  - Удаления товара (`'delete/<int:id>/'`).

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.product_list, name='product_list'),
    path('add/', views.add_product, name='add_product'),
    path('edit/<int:id>/', views.edit_product, name='edit_product'),
    path('delete/<int:id>/', views.delete_product, name='delete_product'),
]
```

#### **7. Реализуйте представления**
- В файле `products/views.py` создайте функции:
  - `product_list` — отображение всех товаров.
  - `add_product` — добавление нового товара.
  - `edit_product` — обновление информации о товаре.
  - `delete_product` — удаление товара.

```python
from django.shortcuts import render, get_object_or_404, redirect
from .models import Product

def product_list(request):
    products = Product.objects.all()
    return render(request, 'products/product_list.html', {'products': products})

def add_product(request):
    if request.method == 'POST':
        name = request.POST['name']
        description = request.POST['description']
        price = request.POST['price']
        Product.objects.create(name=name, description=description, price=price)
        return redirect('product_list')
    return render(request, 'products/add_product.html')

def edit_product(request, id):
    product = get_object_or_404(Product, id=id)
    if request.method == 'POST':
        product.name = request.POST['name']
        product.description = request.POST['description']
        product.price = request.POST['price']
        product.save()
        return redirect('product_list')
    return render(request, 'products/edit_product.html', {'product': product})

def delete_product(request, id):
    product = get_object_or_404(Product, id=id)
    product.delete()
    return redirect('product_list')
```

#### **8. Создайте шаблоны**
- В папке `products/templates/products/` создайте файлы:
  - `product_list.html` для отображения всех товаров.
  - `add_product.html` для добавления нового товара.
  - `edit_product.html` для редактирования товара.

---

## **2. Теоретическая часть: Работа с формами в Django**

### **1. Что такое формы в Django?**
Формы — это способ взаимодействия пользователя с вашим приложением. Django поддерживает два типа форм:
1. **Базовые формы** (`forms.Form`) — для ручной обработки данных.
2. **Модели форм** (`forms.ModelForm`) — для работы с моделями и автоматической привязки данных к базе.

---

### **2. Создание базовой формы**
Пример базовой формы:
```python
from django import forms

class FeedbackForm(forms.Form):
    name = forms.CharField(max_length=100, label="Ваше имя")
    email = forms.EmailField(label="Ваш email")
    message = forms.CharField(widget=forms.Textarea, label="Сообщение")
```

---

### **3. Создание формы, связанной с моделью**
Пример модели:
```python
from django.db import models

class Feedback(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField()
    message = models.TextField()
```

Пример формы:
```python
from django import forms
from .models import Feedback

class FeedbackModelForm(forms.ModelForm):
    class Meta:
        model = Feedback
        fields = ['name', 'email', 'message']
```

---

### **4. Работа с формами в представлениях**
#### **Отображение и обработка данных**
Пример:
```python
from django.shortcuts import render, redirect
from .forms import FeedbackForm

def feedback_view(request):
    if request.method == 'POST':
        form = FeedbackForm(request.POST)
        if form.is_valid():
            # Обработка данных
            return redirect('thank_you')
    else:
        form = FeedbackForm()
    return render(request, 'feedback.html', {'form': form})
```

#### **Привязка формы к данным модели**
Пример:
```python
from .forms import FeedbackModelForm

def feedback_view(request):
    if request.method == 'POST':
        form = FeedbackModelForm(request.POST)
        if form.is_valid():
            form.save()  # Автоматически сохраняет данные в базу
            return redirect('thank_you')
    else:
        form = FeedbackModelForm()
    return render(request, 'feedback.html', {'form': form})
```

---

### **5. Валидация данных**
#### **Встроенные валидаторы**
Пример использования:
```python
from django.core.validators import MinLengthValidator

class FeedbackForm(forms.Form):
    name = forms.CharField(max_length=100, validators=[MinLengthValidator(3)])
```

#### **Пользовательская валидация**
Пример:
```python
class FeedbackForm(forms.Form):
    name = forms.CharField(max_length=100)

    def clean_name(self):
        name = self.cleaned_data['name']
        if "@" in name:
            raise forms.ValidationError("Имя не должно содержать '@'.")
        return name
```

---

### **6. Отображение формы в шаблонах**
#### **Базовый шаблон:**
```html
<form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Отправить</button>
</form>
```

#### **Стилизация формы:**
```html
<form method="post">
    {% csrf_token %}
    <div>
        <label for="{{ form.name.id_for_label }}">{{ form.name.label }}</label>
        {{ form.name }}
    </div>
    <div>
        <label for="{{ form.email.id_for_label }}">{{ form.email.label }}</label>
        {{ form.email }}
    </div>
        <label for="{{ form.message.id_for_label }}">{{ form.message.label }}</label>
        {{ form.message }}
    </div>
    <button type="submit">Отправить</button>
</form>
```

---

### **7. Дополнительные возможности**
#### **Загрузка файлов**
Пример:
```python
file = forms.FileField()
```

#### **Скрытые поля**
Пример:
```python
hidden_field = forms.CharField(widget=forms.HiddenInput(), initial="Скрытое значение")
```

---

### **Заключение**
Работа с формами в Django предоставляет удобный интерфейс для обработки данных. Используйте базовые формы для простых случаев, а `ModelForm` для работы с моделями. 

Если остались вопросы, разберем их на следующем занятии!
