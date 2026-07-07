# SQL-запросы для анализа данных интернет-магазина

## Описание
Этот файл содержит SQL-запросы для работы с базой данных интернет-магазина.
Запросы написаны для решения реальных бизнес-задач: анализ продаж, поиск активных клиентов, мониторинг статусов заказов.

## Структура базы данных
- `products` (id, name, price, category) — товары
- `customers` (id, name, email, city) — клиенты
- `orders` (id, customer_id, total_amount, status, created_at) — заказы
- `order_items` (id, order_id, product_id, quantity) — товары в заказах

---

## Базовые запросы (SELECT, WHERE, ORDER BY, LIMIT)

### 1. Показать все товары дороже 1000 рублей
**Бизнес-контекст:** Маркетолог хочет сделать подборку "Премиум товары" для рассылки.

```sql
SELECT name, price, category
FROM products
WHERE price > 1000
ORDER BY price DESC;
```

### 2. Топ-5 самых дешевых товаров в категории "Электроника"
**Бизнес-контекст:** Маркетолог хочет сделать рассылку "Бюджетная электроника".

```sql
SELECT name, price, category
FROM products
WHERE category = 'Электроника'
ORDER BY price ASC
LIMIT 5;
```

### 3. Посчитать количество заказов со статусом "Доставлен"
**Бизнес-контекст:** Руководство хочет узнать, сколько заказов мы успешно закрыли за всё время.

```sql
SELECT COUNT(*)
FROM orders
WHERE status = 'Доставлен';
```

### 4. Найти клиентов из Москвы или Санкт-Петербурга
**Бизнес-контекст:** Мы запускаем доставку в эти города и хотим сделать по ним отдельную рекламу.

```sql
SELECT name, email, city
FROM customers
WHERE city IN ('Москва', 'Санкт-Петербург');
```

---

## Запросы с JOIN и агрегацией

### 5. Показать имена клиентов и суммы их заказов
**Бизнес-контекст:** Менеджеру по продажам нужен простой отчёт: кто что купил и на какую сумму.

```sql
SELECT customers.name, orders.total_amount
FROM customers
INNER JOIN orders ON customers.id = orders.customer_id;
```

### 6. Посчитать количество заказов каждого клиента
**Бизнес-контекст:** Менеджер по лояльности хочет найти самых активных покупателей для программы скидок.

```sql
SELECT customers.name, COUNT(orders.id) AS orders_count
FROM customers
INNER JOIN orders ON customers.id = orders.customer_id
GROUP BY customers.name;
```

### 7. Посчитать общую сумму покупок каждого клиента (отсортировать по убыванию)
**Бизнес-контекст:** Финансовый отдел хочет понять, кто приносит больше всего выручки.

```sql
SELECT customers.name, SUM(orders.total_amount) AS total_spent
FROM customers
INNER JOIN orders ON customers.id = orders.customer_id
GROUP BY customers.name
ORDER BY total_spent DESC;
```

### 8. Показать, какие товары покупал каждый клиент
**Бизнес-контекст:** Отдел маркетинга хочет сделать персональную рассылку: "Вы покупали этот товар, посмотрите новинки".

```sql
SELECT customers.name, products.name, order_items.quantity
FROM customers
INNER JOIN orders ON customers.id = orders.customer_id
INNER JOIN order_items ON orders.id = order_items.order_id
INNER JOIN products ON products.id = order_items.product_id;
```
### 9. Найти клиентов, которые сделали больше 2 заказов
**Бизнес-контекст:** Менеджер по лояльности ищет самых активных клиентов, чтобы подарить им промокод.

```sql
SELECT customers.name, COUNT(*) AS orders_count
FROM customers
INNER JOIN orders ON customers.id = orders.customer_id
GROUP BY customers.name
HAVING COUNT(*) > 2;
```

### 10. Разделить заказы на категории по сумме
**Бизнес-контекст:** Аналитическому отделу нужно разбить все заказы на три группы: "Мелкий", "Средний" и "Крупный".

```sql
SELECT id, total_amount,
  CASE
    WHEN total_amount < 1000 THEN 'Мелкий'
    WHEN total_amount >= 1000 AND total_amount <= 5000 THEN 'Средний'
    WHEN total_amount > 5000 THEN 'Крупный'
    ELSE 'Неизвестный'
  END AS order_category
FROM orders;
```

## Используемые навыки SQL

| Тема SQL | Запросы |
|----------|---------|
| SELECT, FROM, WHERE | 1, 2, 3, 4 |
| ORDER BY, LIMIT | 1, 2, 7 |
| COUNT, SUM | 3, 6, 7, 9 |
| INNER JOIN | 5, 6, 7, 8, 9 |
| GROUP BY | 6, 7, 9 |
| HAVING | 9 |
| CASE WHEN | 10 |
| IN | 4 |
