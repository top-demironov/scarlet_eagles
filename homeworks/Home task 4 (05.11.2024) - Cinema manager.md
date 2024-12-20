# Домашнее задание для группы "Алые орлы": Разработка системы управления кинотеатром

---

#### **Цель задания**

1. Закрепить навыки работы с моделями и ORM в Django.
2. Создать систему управления книгами и их аренды.
3. Изучить отношения между моделями: "Один ко многим" и "Многие ко многим".

---

### **Описание задания**

Создайте систему управления кинотеатром. Она должна включать следующие сущности:

1. **Фильмы:** информация о фильме, его названии, длительности, жанре и дате выхода.
2. **Залы:** залы кинотеатра, включая их название и вместимость.
3. **Сеансы:** расписание киносеансов, включая зал, фильм и время показа.
4. **Бронирование:** информация о том, кто и на какой сеанс забронировал билеты.

---

### **Требования к выполнению**

#### **Модели**

1. **Модель `Movie` (Фильм):**
   - Поля:
     - `title` (название фильма).
     - `duration` (длительность фильма в минутах).
     - `genre` (жанр фильма).
     - `release_date` (дата выхода фильма).

2. **Модель `Hall` (Зал):**
   - Поля:
     - `name` (название зала).
     - `capacity` (вместимость зала).

3. **Модель `Session` (Сеанс):**
   - Поля:
     - `movie` (фильм).
     - `hall` (зал).
     - `show_time` (время показа).

---

#### **Этапы выполнения**

1. **Создайте приложение `cinema`:**     
2. **Определите модели:**
    - Создайте четыре модели: `Movie`, `Hall` и `Session`, используя предложенные поля и связи.
3. **Настройте миграции:**
    - Создайте и примените миграции для ваших моделей.
4. **Добавьте данные через Django Admin или Django Shell:**    
    - Создайте несколько фильмов, залов, и сеансов.
5. **Реализуйте функциональность через Django ORM:**
    - Выведите список всех предстоящих сеансов.
    - Фильтруйте сеансы по фильму или залу.
    - Получите список всех сеансов для конкретного зала.
6. **Создайте html-шаблоны**
   - Отобразите в них данные полученные из базы данных, по каждой сущности по аналогии с классной работой

---

#### **Дополнительное задание (по желанию):**

1. Добавьте поле price (цена билета) в модель Session.
2. Реализуйте подсчёт общей стоимости бронирования.
3. Настройте админ-панель для удобного управления расписанием сеансов и бронированиями.

---

### **Итог**

После выполнения задания вы:

1. Освоите создание и настройку моделей с различными типами связей.
2. Научитесь использовать Django ORM для работы с данными.
3. Создадите функциональную систему управления библиотекой.

Если возникнут вопросы, обсудим их на следующем занятии!

## В завершение предлагаю пройти опрос качества методического материала
https://forms.gle/224CibgUdUJPbi6u9