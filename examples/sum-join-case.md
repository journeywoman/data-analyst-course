## В предположении, что приход и расход денег на каждом пункте приема фиксируется произвольное число раз 
(первичным ключом в таблицах является столбец code), требуется получить таблицу, в которой каждому пункту за каждую дату
выполнения операций будет соответствовать одна строка.
Вывод: point, date, суммарный расход пункта за день (out), суммарный приход пункта за день (inc). Отсутствующие значения считать неопределенными (NULL).
```sql
with inc as
(Select point, date, sum (inc) as inc
from income
group by point, date)
,
out as
(select point, date, sum (out) as out
from outcome
group by point, date)
select case when inc.point is not null then inc.point else out.point end  as point
, case when inc.date is not null then inc.date else out.date end as date, out, inc
from inc
full join out on inc.point = out.point
             and inc.date = out.date
```
