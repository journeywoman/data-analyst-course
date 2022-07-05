Создание, изменение и удаление таблиц
```sql
create table lesson_8.emploees_vlasovava -- создание таблицы
(id int primary key 
, name text
, last_name text
, middle_name text
, position text
, salary float
) -- название столбцов, которые нужно добавить

select *
from lesson_8.emploees_vlasovava

insert into lesson_8.emploees_vlasovava (id, name) -- добавление данных в таблицу
values (1, 'Test')

alter table lesson_8.emploees_vlasovava -- изменение таблицы
add column department text -- добавление столбца

create local temp table tt_test as -- создание временной таблицы
select *
from lesson_8.emploees_vlasovava

select *
from tt_test

truncate table tt_test -- очистка таблицы

drop table tt_test -- удаление временной таблицы

truncate table lesson_8.emploees_vlasovava

insert into lesson_8.emploees_vlasovava (id, name, last_name, middle_name, position, salary, department)
values (1, 'Ivan', 'Ivanov', 'Ivanovich', 'Director', 1000000 , 'Management')

insert into lesson_8.emploees_vlasovava (id, name, last_name, middle_name, position, salary, department) -- второй способ добавления данных
select 2, 'Maria', 'Petrova', 'Olegovna', 'Commertial director', 800000, 'Management'
union all
select 3, 'Petr', 'Petrov', 'Petrovich', 'IT specialist', 300000, 'IT'

create table lesson_8.emploees_null_test_vlasovava as
select *
from lesson_8.emploees_vlasovava

select *
from lesson_8.emploees_empty_test_vlasovava

update lesson_8.emploees_null_test_vlasovava -- обновление данных
set name = null
  , last_name = null
  , middle_name = null
where id = 3

create table lesson_8.emploees_empty_test_vlasovava as
select *
from lesson_8.emploees_vlasovava

delete 
from lesson_8.emploees_empty_test_vlasovava -- удаление строк, если в where не задать условия, то удалятся все строки
where id = 1
```
