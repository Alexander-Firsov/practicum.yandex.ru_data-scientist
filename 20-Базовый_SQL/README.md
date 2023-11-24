
<table>
<tr>
<th>Задача 1</th>
</tr>
<tr><td>Отобразите все записи из таблицы company по компаниям, которые закрылись.</td></tr>
<tr><td>

```sql
SELECT *
FROM company
WHERE status = 'closed'
```
</tr></td>
</table>

<table>
<tr>
<th>Задача 2</th>
</tr>
<tr><td>Отобразите количество привлечённых средств для новостных компаний США. Используйте данные из таблицы `company`. Отсортируйте таблицу по убыванию значений в поле `funding_total`.</td></tr>
<tr><td>

```sql
SELECT funding_total
FROM company
WHERE category_code = 'news' AND country_code = 'USA'
ORDER BY funding_total DESC
```
</tr></td>
</table>


<table>
<tr>
<th>Задача 3</th>
</tr>
<tr><td>Найдите общую сумму сделок по покупке одних компаний другими в долларах. Отберите сделки, которые осуществлялись только за наличные с 2011 по 2013 год включительно.</td></tr>
<tr><td>

```sql
SELECT SUM(price_amount)
FROM acquisition
WHERE term_code = 'cash' AND acquired_at BETWEEN '20110101' AND '20131231'
```
</tr></td>
</table>

<table>
<tr>
<th>Задача 4</th>
</tr>
<tr><td>Отобразите имя, фамилию и названия аккаунтов людей в поле network_username, у которых названия аккаунтов начинаются на `Silver`.</td></tr>
<tr><td>

```sql
SELECT first_name, last_name, network_username
FROM people
WHERE network_username LIKE 'Silver%'
```
</tr></td>
</table>

<table>
<tr>
<th>Задача 5</th>
</tr>
<tr><td>Выведите на экран всю информацию о людях, у которых названия аккаунтов в поле network_username содержат подстроку `money`, а фамилия начинается на `K`.</td></tr>
<tr><td>

```sql
SELECT *
FROM people
WHERE network_username LIKE '%money%' AND last_name LIKE 'K%'
```
</tr></td>
</table>

<table>
<tr>
<th>Задача 6</th>
</tr>
<tr><td>Для каждой страны отобразите общую сумму привлечённых инвестиций, которые получили компании, зарегистрированные в этой стране. Страну, в которой зарегистрирована компания, можно определить по коду страны. Отсортируйте данные по убыванию суммы.</td></tr>
<tr><td>

```sql
SELECT country_code, SUM(funding_total)
FROM company
GROUP BY country_code
ORDER BY sum DESC
```
</tr></td>
</table>

<table>
<tr>
<th>Задача 7</th>
</tr>
<tr><td>Составьте таблицу, в которую войдёт дата проведения раунда, а также минимальное и максимальное значения суммы инвестиций, привлечённых в эту дату.<br>
Оставьте в итоговой таблице только те записи, в которых минимальное значение суммы инвестиций не равно нулю и не равно максимальному значению.</td></tr>
<tr><td>

```sql
SELECT funded_at,
  MIN(raised_amount) AS min,
  MAX(raised_amount) AS max
FROM funding_round
GROUP BY funded_at
HAVING MIN(raised_amount) NOT IN (0, MAX(raised_amount))
```
</tr></td>
</table>

<table>
<tr>
<th>Задача 8</th>
</tr>
<tr><td>

Создайте поле с категориями:  
    * Для фондов, которые инвестируют в 100 и более компаний, назначьте категорию high_activity.  
    * Для фондов, которые инвестируют в 20 и более компаний до 100, назначьте категорию middle_activity.  
    * Если количество инвестируемых компаний фонда не достигает 20, назначьте категорию low_activity.  
    Отобразите все поля таблицы fund и новое поле с категориями.
    </td></tr>
<tr><td>

```sql
SELECT *,
    CASE
        WHEN invested_companies < 20 THEN 'low_activity'
        WHEN invested_companies < 100 THEN 'middle_activity'
        WHEN invested_companies >= 100 THEN 'high_activity'
    END AS Ы
FROM fund
```
</tr></td>
</table>

<table>
<tr>
<th>Задача 9</th>
</tr>
<tr><td>Для каждой из категорий, назначенных в предыдущем задании, посчитайте округлённое до ближайшего целого числа среднее количество инвестиционных раундов, в которых фонд принимал участие. Выведите на экран категории и среднее число инвестиционных раундов. Отсортируйте таблицу по возрастанию среднего.</td></tr>
<tr><td>

```sql
WITH
Ы AS (SELECT investment_rounds,
       CASE
           WHEN invested_companies>=100 THEN 'high_activity'
           WHEN invested_companies>=20 THEN 'middle_activity'
           ELSE 'low_activity'
       END AS Ж--activity
       --, *
FROM fund)
SELECT ROUND(AVG(investment_rounds)) AS Ё, Ж
FROM Ы
GROUP BY Ж
ORDER BY Ё
```
</tr></td>
</table>

<table>
<tr>
<th>Задача 10</th>
</tr>
<tr><td>

Проанализируйте, в каких странах находятся фонды, которые чаще всего инвестируют в стартапы.  
Для каждой страны посчитайте минимальное, максимальное и среднее число компаний, в которые инвестировали фонды этой страны, основанные с 2010 по 2012 год включительно. Исключите страны с фондами, у которых минимальное число компаний, получивших инвестиции, равно нулю.  
Выгрузите десять самых активных стран-инвесторов: отсортируйте таблицу по среднему количеству компаний от большего к меньшему. Затем добавьте сортировку по коду страны в лексикографическом порядке.
</td></tr>
<tr><td>

```sql
SELECT country_code,
       MIN(invested_companies),
       MAX(invested_companies),
       AVG(invested_companies)
FROM fund
WHERE EXTRACT (YEAR FROM founded_at) BETWEEN 2010 AND 2012
GROUP BY country_code
HAVING MIN(invested_companies) > 0
ORDER BY AVG(invested_companies) DESC
LIMIT 10;
```
</tr></td>
</table>

<table>
<tr>
<th>Задача 11</th>
</tr>
<tr><td>Отобразите имя и фамилию всех сотрудников стартапов. Добавьте поле с названием учебного заведения, которое окончил сотрудник, если эта информация известна.</td></tr>
<tr><td>

```sql
SELECT first_name, last_name, instituition
FROM people
LEFT JOIN education ON people.id = education.person_id
```
</tr></td>
</table>

<table>
<tr>
<th>Задача 12</th>
</tr>
<tr><td>Для каждой компании найдите количество учебных заведений, которые окончили её сотрудники. Выведите название компании и число уникальных названий учебных заведений. Составьте топ-5 компаний по количеству университетов.</td></tr>
<tr><td>

```sql
SELECT /*company.id,*/ company.name, COUNT(DISTINCT instituition)
FROM company
RIGHT JOIN people ON people.company_id = company.id
JOIN education ON people.id = education.person_id
GROUP BY company.id, company.name
HAVING company.id IS NOT NULL
ORDER BY count DESC
LIMIT 5
```
</tr></td>
</table>

<table>
<tr>
<th>Задача 13</th>
</tr>
<tr><td>Составьте список с уникальными названиями закрытых компаний, для которых первый раунд финансирования оказался последним.</td></tr>
<tr><td>

```sql
SELECT DISTINCT name-- , status, is_first_round , is_last_round
FROM company
LEFT JOIN funding_round AS fr ON fr.company_id = company.id
WHERE (status = 'closed')  AND (is_last_round = 1) AND (is_first_round = 1)
```
</tr></td>
</table>

<table>
<tr>
<th>Задача 14</th>
</tr>
<tr><td>
Составьте список уникальных номеров сотрудников, которые работают в компаниях, отобранных в предыдущем задании.</td></tr>
<tr><td>

```sql
with
    comp AS (SELECT DISTINCT name, company.id
    FROM company
    LEFT JOIN funding_round AS fr ON fr.company_id = company.id
    WHERE (status = 'closed')  AND (is_last_round = 1) AND (is_first_round = 1))
select people.id
FROM people
JOIN comp ON comp.id=people.company_id
```
</tr></td>
</table>

<table>
<tr>
<th>Задача 15</th>
</tr>
<tr><td>
Составьте таблицу, куда войдут уникальные пары с номерами сотрудников из предыдущей задачи и учебным заведением, которое окончил сотрудник.</td></tr>
<tr><td>

```sql
with
    comp AS (SELECT DISTINCT name, company.id
    FROM company
    LEFT JOIN funding_round AS fr ON fr.company_id = company.id
    WHERE (status = 'closed')  AND (is_last_round = 1) AND (is_first_round = 1)),
    
    peop AS (select people.id
    FROM people
    JOIN comp ON comp.id=people.company_id)
SELECT DISTINCT peop.id, instituition
FROM peop
JOIN education ON peop.id = education.person_id
```
</tr></td>
</table>

<table>
<tr>
<th>Задача 16</th>
</tr>
<tr><td>
Посчитайте количество учебных заведений для каждого сотрудника из предыдущего задания. При подсчёте учитывайте, что некоторые сотрудники могли окончить одно и то же заведение дважды.</td></tr>
<tr><td>

```sql
with
    comp AS (SELECT DISTINCT name, company.id
    FROM company
    LEFT JOIN funding_round AS fr ON fr.company_id = company.id
    WHERE (status = 'closed')  AND (is_last_round = 1) AND (is_first_round = 1)),
    
    peop AS (select people.id
    FROM people
    JOIN comp ON comp.id=people.company_id)
    
SELECT DISTINCT peop.id, COUNT(instituition)
FROM peop
JOIN education ON peop.id = education.person_id
GROUP BY peop.id
```
</tr></td>
</table>

<table>
<tr>
<th>Задача 17</th>
</tr>
<tr><td>
Дополните предыдущий запрос и выведите среднее число учебных заведений (всех, не только уникальных), которые окончили сотрудники разных компаний. Нужно вывести только одну запись, группировка здесь не понадобится.</td></tr>
<tr><td>

```sql
with
    comp AS (SELECT DISTINCT name, company.id
    FROM company
    LEFT JOIN funding_round AS fr ON fr.company_id = company.id
    WHERE (status = 'closed')  AND (is_last_round = 1) AND (is_first_round = 1)),
    
    peop AS (select people.id
    FROM people
    JOIN comp ON comp.id=people.company_id),
    
    query_3 AS (SELECT DISTINCT peop.id, COUNT(instituition)
    FROM peop
    JOIN education ON peop.id = education.person_id
    GROUP BY peop.id)
SELECT AVG(count)
FROM query_3
```
</tr></td>
</table>

<table>
<tr>
<th>Задача 18</th>
</tr>
<tr><td>
Напишите похожий запрос: выведите среднее число учебных заведений (всех, не только уникальных), которые окончили сотрудники Socialnet.</td></tr>
<tr><td>

```sql
with
    comp AS (SELECT DISTINCT name, company.id
    FROM company
    LEFT JOIN funding_round AS fr ON fr.company_id = company.id
    WHERE company.name = 'Socialnet'),--(status = 'closed')  AND (is_last_round = 1) AND (is_first_round = 1)),
    
    peop AS (select people.id
    FROM people
    JOIN comp ON comp.id=people.company_id),
    
    query_3 AS (SELECT DISTINCT peop.id, COUNT(instituition)
    FROM peop
    JOIN education ON peop.id = education.person_id
    GROUP BY peop.id)
SELECT AVG(count)
FROM query_3
```
</tr></td>
</table>

<table>
<tr>
<th>Задача 19</th>
</tr>
<tr><td>

Составьте таблицу из полей:  
`name_of_fund` — название фонда;  
`name_of_company` — название компании;  
`amount` — сумма инвестиций, которую привлекла компания в раунде.  
В таблицу войдут данные о компаниях, в истории которых было больше шести важных этапов, а раунды финансирования проходили с 2012 по 2013 год включительно.</td></tr>
<tr><td>

```sql
SELECT f.name AS name_of_fund,
    company.name AS name_of_company,
    fr.raised_amount AS amount
FROM company
JOIN investment AS i ON i.company_id = company.id
JOIN fund AS f ON i.fund_id = f.id
JOIN funding_round AS fr ON fr.id = i.funding_round_id -- fr.company_id = company.id
WHERE company.milestones > 6 AND fr.funded_at BETWEEN '20120101' AND '20131231'
```
</tr></td>
</table>

<table>
<tr>
<th>Задача 20</th>
</tr>
<tr><td>

Выгрузите таблицу, в которой будут такие поля:  
* название компании-покупателя;  
* сумма сделки;  
* название компании, которую купили;  
* сумма инвестиций, вложенных в купленную компанию;  
* доля, которая отображает, во сколько раз сумма покупки превысила сумму вложенных в компанию инвестиций, округлённая до ближайшего целого числа.  
Не учитывайте те сделки, в которых сумма покупки равна нулю. Если сумма инвестиций в компанию равна нулю, исключите такую компанию из таблицы.  
Отсортируйте таблицу по сумме сделки от большей к меньшей, а затем по названию купленной компании в лексикографическом порядке. Ограничьте таблицу первыми десятью записями.</td></tr>
<tr><td>

```sql
WITH
acquiring AS(SELECT c.name AS buyer,
    a.price_amount AS price,
    a.id AS KEY
    FROM acquisition AS a
    LEFT JOIN company AS c ON a.acquiring_company_id = c.id
    WHERE a.price_amount > 0),
    
acquired AS (SELECT c.name AS acquisition,
    c.funding_total AS investment,
    a.id AS KEY
    FROM acquisition AS a
    LEFT JOIN company AS c ON a.acquired_company_id = c.id
    WHERE c.funding_total > 0)

SELECT acqn.buyer,
acqn.price,
acqd.acquisition,
acqd.investment,
ROUND(acqn.price / acqd.investment) AS uplift
FROM acquiring AS acqn
JOIN acquired AS acqd ON acqn.KEY = acqd.KEY
ORDER BY price DESC, acquisition
LIMIT 10;
```
</tr></td>
</table>

<table>
<tr>
<th>Задача 21</th>
</tr>
<tr><td>
Выгрузите таблицу, в которую войдут названия компаний из категории `social`, получившие финансирование с 2010 по 2013 год включительно. Проверьте, что сумма инвестиций не равна нулю. Выведите также номер месяца, в котором проходил раунд финансирования.</td></tr>
<tr><td>

```sql
SELECT 
        c.name AS c_name,
        EXTRACT(MONTH FROM CAST(fr.funded_at AS date)) AS month
    FROM company AS c
    RIGHT JOIN funding_round AS fr ON c.id = fr.company_id
    WHERE c.category_code = 'social' AND fr.raised_amount != 0
        AND fr.funded_at BETWEEN '20100101' AND '20131231'
    WINDOW rouns AS (PARTITION BY c.id )
```
</tr></td>
</table>

<table>
<tr>
<th>Задача 22</th>
</tr>
<tr><td>
Отберите данные по месяцам с 2010 по 2013 год, когда проходили инвестиционные раунды. Сгруппируйте данные по номеру месяца и получите таблицу, в которой будут поля:  
* номер месяца, в котором проходили раунды;  
* количество уникальных названий фондов из США, которые инвестировали в этом месяце;  
* количество компаний, купленных за этот месяц;  
* общая сумма сделок по покупкам в этом месяце.</td></tr>
<tr><td>

```sql
WITH
fundings AS (SELECT EXTRACT (MONTH FROM CAST(fr.funded_at AS DATE)) AS funding_month,
      COUNT(DISTINCT f.id) AS id_fund
FROM fund AS f     
LEFT JOIN investment AS i ON f.id=i.fund_id
LEFT JOIN funding_round AS fr ON i.funding_round_id=fr.id
WHERE f.country_code = 'USA'
  AND EXTRACT(YEAR FROM CAST (fr.funded_at AS DATE)) BETWEEN 2010 AND 2013
GROUP BY funding_month),
 
acquisitions AS (SELECT EXTRACT (MONTH FROM CAST(acquired_at AS DATE)) AS funding_month,
      COUNT(acquired_company_id) AS acquired,
      SUM(price_amount) AS sum_total
FROM acquisition
WHERE EXTRACT(YEAR FROM CAST (acquired_at AS DATE)) BETWEEN 2010 AND 2013
GROUP BY funding_month) 
SELECT fnd.funding_month,
       fnd.id_fund,
       acq.acquired,
       acq.sum_total
FROM fundings AS fnd 
LEFT JOIN acquisitions AS acq ON fnd.funding_month=acq.funding_month
```
</tr></td>
</table>

<table>
<tr>
<th>Задача 23</th>
</tr>
<tr><td>
Составьте сводную таблицу и выведите среднюю сумму инвестиций для стран, в которых есть стартапы, зарегистрированные в 2011, 2012 и 2013 годах. Данные за каждый год должны быть в отдельном поле. Отсортируйте таблицу по среднему значению инвестиций за 2011 год от большего к меньшему.</td></tr>
<tr><td>

```sql
WITH
inv_2011 AS (SELECT country_code,
        AVG(funding_total) AS totalavg_2011
    FROM company
    WHERE EXTRACT(YEAR FROM CAST(founded_at AS DATE)) = 2011
    GROUP BY country_code),
    
inv_2012 AS (SELECT country_code,
        AVG(funding_total) AS totalavg_2012
    FROM company
    WHERE EXTRACT(YEAR FROM CAST(founded_at AS DATE)) = 2012
    GROUP BY country_code),
    
inv_2013 AS (SELECT country_code,
        AVG(funding_total) AS totalavg_2013
    FROM company
    WHERE EXTRACT(YEAR FROM CAST(founded_at AS DATE)) = 2013
    GROUP BY country_code)
    
SELECT inv_2011.country_code,
       inv_2011.totalavg_2011,
       inv_2012.totalavg_2012,
       inv_2013.totalavg_2013
FROM inv_2011
INNER JOIN inv_2012 ON inv_2011.country_code = inv_2012.country_code
INNER JOIN inv_2013 ON inv_2011.country_code = inv_2013.country_code
ORDER BY totalavg_2011 DESC
```
</tr></td>
</table>
