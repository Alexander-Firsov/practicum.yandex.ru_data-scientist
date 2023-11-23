|Задача 1|
|---|
|Отобразите все записи из таблицы company по компаниям, которые закрылись.|

```sql
SELECT *
FROM company
WHERE status = 'closed'
```

|Задача 2|
|---|
|Отобразите количество привлечённых средств для новостных компаний США. Используйте данные из таблицы `company`. Отсортируйте таблицу по убыванию значений в поле `funding_total`.|

```sql
SELECT funding_total
FROM company
WHERE category_code = 'news' AND country_code = 'USA'
ORDER BY funding_total DESC
```

|Задача 3|
|---|
|Найдите общую сумму сделок по покупке одних компаний другими в долларах. Отберите сделки, которые осуществлялись только за наличные с 2011 по 2013 год включительно.|

```sql
SELECT SUM(price_amount)
FROM acquisition
WHERE term_code = 'cash' AND acquired_at BETWEEN '20110101' AND '20131231'
```

|Задача 4|
|---|
|Отобразите имя, фамилию и названия аккаунтов людей в поле network_username, у которых названия аккаунтов начинаются на `Silver`.|

```sql
SELECT first_name, last_name, network_username
FROM people
WHERE network_username LIKE 'Silver%'
```

|Задача 5|
|---|
|Выведите на экран всю информацию о людях, у которых названия аккаунтов в поле network_username содержат подстроку `money`, а фамилия начинается на `K`.|

```sql
SELECT *
FROM people
WHERE network_username LIKE '%money%' AND last_name LIKE 'K%'
```

|Задача 6|
|---|
|Для каждой страны отобразите общую сумму привлечённых инвестиций, которые получили компании, зарегистрированные в этой стране. Страну, в которой зарегистрирована компания, можно определить по коду страны. Отсортируйте данные по убыванию суммы.|

```sql
SELECT country_code, SUM(funding_total)
FROM company
GROUP BY country_code
ORDER BY sum DESC
```

|Задача 7|
|---|
|Составьте таблицу, в которую войдёт дата проведения раунда, а также минимальное и максимальное значения суммы инвестиций, привлечённых в эту дату.  
Оставьте в итоговой таблице только те записи, в которых минимальное значение суммы инвестиций не равно нулю и не равно максимальному значению.|

```sql
SELECT funded_at,
  MIN(raised_amount) AS min,
  MAX(raised_amount) AS max
FROM funding_round
GROUP BY funded_at
HAVING MIN(raised_amount) NOT IN (0, MAX(raised_amount))
```

|Задача 8|
|---|
|Создайте поле с категориями:  
    * Для фондов, которые инвестируют в 100 и более компаний, назначьте категорию high_activity.  
    * Для фондов, которые инвестируют в 20 и более компаний до 100, назначьте категорию middle_activity.  
    * Если количество инвестируемых компаний фонда не достигает 20, назначьте категорию low_activity.  
    Отобразите все поля таблицы fund и новое поле с категориями.|


```sql
SELECT *,
    CASE
        WHEN invested_companies < 20 THEN 'low_activity'
        WHEN invested_companies < 100 THEN 'middle_activity'
        WHEN invested_companies >= 100 THEN 'high_activity'
    END AS Ы
FROM fund
```

! **Language**
  ! **Pros**
    ! **Cons**
! MarkedText
  ! - Convenient editing of tables.
  ! - Support for complex formatting inside cells.
  ! - Ease of implementation.
  ! - Easy-to-remember consistent syntax.
  ! - Ease of editing in a non-English keyboard layout.
  ! - The columns do not spread far to the right for horizontal scrolling and are not transferred to a new line.
    ! - There is no broad support by third-party tools.
! MarkDown
  ! - Extensive support with various tools.
  ! - A human-readable representation of simple tables.
    ! - Difficulties with editing tables.
    ! - Highly restricted cell contents.
