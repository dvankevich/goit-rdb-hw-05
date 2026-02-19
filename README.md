# goit-rdb-hw-05

## 1. Напишіть SQL запит, який буде відображати таблицю order_details та поле customer_id з таблиці orders відповідно для кожного поля запису з таблиці order_details.

```sql
SELECT
	od.id,
	od.order_id,
	od.product_id,
	od.quantity,
	(SELECT o.customer_id FROM orders o WHERE o.id = od.order_id) AS customer_id
FROM
	order_details od;
```

```
+------+----------+------------+----------+-------------+
| id   | order_id | product_id | quantity | customer_id |
+------+----------+------------+----------+-------------+
|    1 |    10248 |         11 |       12 |          90 |
|    2 |    10248 |         42 |       10 |          90 |
|    3 |    10248 |         72 |        5 |          90 |
|    4 |    10249 |         14 |        9 |          81 |
|    5 |    10249 |         51 |       40 |          81 |
|    6 |    10250 |         41 |       10 |          34 |
|    7 |    10250 |         51 |       35 |          34 |
|    8 |    10250 |         65 |       15 |          34 |
|    9 |    10251 |         22 |        6 |          84 |
|   10 |    10251 |         57 |       15 |          84 |
```

## 2. Напишіть SQL запит, який буде відображати таблицю order_details.

Відфільтруйте результати так, щоб відповідний запис із таблиці orders виконував умову shipper_id=3.

Це має бути зроблено за допомогою вкладеного запиту в операторі WHERE.

```sql
SELECT *
FROM order_details od
WHERE od.order_id IN (
    SELECT o.id
    FROM orders o
    WHERE o.shipper_id = 3
);
```

```
+------+----------+------------+----------+
| id   | order_id | product_id | quantity |
+------+----------+------------+----------+
|    1 |    10248 |         11 |       12 |
|    2 |    10248 |         42 |       10 |
|    3 |    10248 |         72 |        5 |
|   21 |    10255 |          2 |       20 |
|   22 |    10255 |         16 |       35 |
|   23 |    10255 |         36 |       25 |
|   24 |    10255 |         59 |       30 |
|   27 |    10257 |         27 |       25 |
|   28 |    10257 |         39 |        6 |
|   29 |    10257 |         77 |       15 |
```

## 3. Напишіть SQL запит, вкладений в операторі FROM, який буде обирати рядки з умовою quantity>10 з таблиці order_details.

Для отриманих даних знайдіть середнє значення поля quantity — групувати слід за order_id.

```sql
SELECT
    od.order_id,
    AVG(od.quantity) AS avg_quantity
FROM (
    SELECT *
    FROM order_details
    WHERE quantity > 10
) AS od
GROUP BY od.order_id;
```

```
+----------+--------------+
| order_id | avg_quantity |
+----------+--------------+
|    10248 |      12.0000 |
|    10249 |      40.0000 |
|    10250 |      25.0000 |
|    10251 |      17.5000 |
|    10252 |      35.0000 |
|    10253 |      34.0000 |
|    10254 |      19.0000 |
|    10255 |      27.5000 |
|    10256 |      13.5000 |
|    10257 |      20.0000 |
```

## 4. Розв’яжіть завдання 3, використовуючи оператор WITH для створення тимчасової таблиці temp.

Якщо ваша версія MySQL більш рання, ніж 8.0, створіть цей запит за аналогією до того, як це зроблено в конспекті.

```sql
WITH temp AS (
    SELECT *
    FROM order_details
    WHERE quantity > 10
)
SELECT
    temp.order_id,
    AVG(temp.quantity) AS avg_quantity
FROM temp
GROUP BY temp.order_id;
```

```
+----------+--------------+
| order_id | avg_quantity |
+----------+--------------+
|    10248 |      12.0000 |
|    10249 |      40.0000 |
|    10250 |      25.0000 |
|    10251 |      17.5000 |
|    10252 |      35.0000 |
|    10253 |      34.0000 |
|    10254 |      19.0000 |
|    10255 |      27.5000 |
|    10256 |      13.5000 |
|    10257 |      20.0000 |

```

## 5. Створіть функцію з двома параметрами, яка буде ділити перший параметр на другий.

```sql
DROP FUNCTION IF EXISTS divide_floats;

DELIMITER //

CREATE FUNCTION divide_floats(num1 FLOAT, num2 FLOAT)
RETURNS FLOAT
BEGIN
    RETURN num1 / num2;
END //

DELIMITER ;
```

### Використання функції

```sql
SELECT
    od.id,
    divide_floats(od.quantity, 2.0) AS result
FROM
    order_details od;
```

```
+------+--------+
| id   | result |
+------+--------+
|    1 |      6 |
|    2 |      5 |
|    3 |    2.5 |
|    4 |    4.5 |
|    5 |     20 |
|    6 |      5 |
|    7 |   17.5 |
|    8 |    7.5 |
|    9 |      3 |
|   10 |    7.5 |
```
