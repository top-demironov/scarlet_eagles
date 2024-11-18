
# Система управления заметками (Домашнее задание для группы "Алые орлы")

## Цель
Данное задание направлено на знакомство с основами Django, включая:

- Создание моделей данных.
- Работа с базой данных через ORM Django.
- Настройка маршрутов, представлений и шаблонов.
- Разработка функционального приложения для управления заметками.

## Описание задания
Ваша группа должна разработать систему управления заметками, которая позволяет:

1. Создавать заметки.
2. Отображать список всех заметок.
3. Удалять заметки.

## Этапы выполнения

### 1. Создайте проект Django
- Создайте новый проект под названием `notemanager`.

### 2. Создайте приложение
- Внутри проекта создайте приложение `notes`.

### 3. Настройте приложение
- Добавьте `notes` в список `INSTALLED_APPS` в файле `settings.py`.

### 4. Определите модель данных
- В файле `notes/models.py` создайте модель `Note` с полями:
  - `title`: Заголовок заметки (строка, максимальная длина 200 символов).
  - `content`: Содержимое заметки (текстовое поле).
  - `created_at`: Дата создания заметки (автоматически создаваемое поле).


### 5. Примените миграции
- Создайте и примените миграции для новой модели:
  ```bash
  python manage.py makemigrations
  python manage.py migrate
  ```

### 6. Настройте маршруты
- Создайте файл `notes/urls.py` и настройте следующие маршруты:
  - `''`: Для отображения списка заметок.
  - `'add/'`: Для добавления новой заметки.
  - `'delete/<int:id>/'`: Для удаления заметки.


### 7. Создайте представления
- В файле `notes/views.py` реализуйте следующие функции:
  - `note_list`: Отображает список всех заметок.
  - `add_note`: Позволяет добавлять новую заметку.
  - `delete_note`: Удаляет выбранную заметку.

### 8. Создайте шаблоны
- В папке `notes/templates/notes/` создайте файл `note_list.html` для отображения списка заметок.
- Включите в шаблон форму для добавления новых заметок.

---

## Ожидаемый результат
В результате выполнения задания каждый участник группы должен понять:

1. Как работать с моделями, представлениями и шаблонами в Django.
2. Как использовать маршруты и взаимодействовать с базой данных.

Если у вас возникнут вопросы по выполнению задания, обсудите их внутри группы. Удачи!