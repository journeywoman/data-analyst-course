## Найти причину низкого показателя ключевой метрики апрельской когорты
```sql
/*
Таблица classes
В этой таблице мы промаркируем когорты и вычислим время их жизни.
Возьмем за основу структуру базовой таблицы (skyeng_db.classes), просто добавим два столбца (cohort и LT month).
*/


with
classes as
(select *
    , date_trunc('month', min(class_start_datetime) over (partition by user_id)) as cohort
    -- , date_trunc('month', class_start_datetime) - date_trunc('month', min(class_start_datetime) over (partition by user_id))
    , extract(year from age(date_trunc('month',class_start_datetime),date_trunc('month', min(class_start_datetime) over (partition by user_id))))*12 +
    extract(month from age(date_trunc('month',class_start_datetime),date_trunc('month', min(class_start_datetime) over (partition by user_id)))) as LT_month
from skyeng_db.classes
order by user_id, class_start_datetime
)

/*
Таблица cohort_sizes
Здесь мы должны посчитать кол-во людей в каждой когорте.
Эта таблица небольшая, кол-во строчек равняется кол-ву когорт.
Когорты дальше июня нас не интересуют!
*/
,
cohort_sizes as 
(select
    cohort
    ,count(distinct user_id) as cohort_sizes
from
    classes
where cohort < '2016-07-01'
    and class_status in ('success', 'failed_by_student')
    and class_type != 'trial'
group by cohort)

/*
Таблица cohort_chrs
Тут мы посчитаем кол-во занятий и активных студентов в каждой когорте в разрезе месяцев жизни.
У каждой когорты будет несколько пережитых месяцев (начиная с 0).
В каждом месяце жизни нас интересует число проведенных уроков и число активных студентов.
Когорты дальше июня нас не интересуют!
*/
,
cohort_chrs as
(select cohort, lt_month
    , count(id_class) as cohort_classes
    , count(distinct user_id) as active_students
from classes
where cohort < '2016-07-01'
    and class_status in ('success', 'failed_by_student')
    and class_type != 'trial'
group by cohort, lt_month
order by 1, 2)

/*
Таблица cohort_life
Здесь добавим к предыдущей таблице (cohort_chrs) таблицу с размерами (cohort_sizes)
Там мы найдем:
- долю активных студентов относительно изначальной когорты;
- сколько пришлось занятий в каждой когороте каждого месяца её жизни;
- сколько в среднем пришлось занятий на одного студента в каждой когороте каждого месяца жизни;
- сколько в среднем пришлось занятий на одного активного студента в каждой когороте каждого месяца жизни.
В каждом месяце жизни нас интересует число проведенных уроков и число активных студентов
Когорты дальше июня нас не интересуют!
*/
,
cohort_life as
(select cohort, lt_month, cohort_sizes, cohort_classes, active_students
    , active_students*1.0/cohort_sizes as active_students_share
    , cohort_classes*1.0/cohort_sizes as intensity
    , cohort_classes*1.0/active_students as intensity_active
from cohort_sizes
join cohort_chrs using (cohort)
order by 1, 2)

--------------------------------------------------- Графический блок ---------------------------------------------------
/*
График "Уроки на 1 студента".
Иллюстрирует активность студентов (classes_per_student) в течение времени жизни каждой когорты (lt_month)
*/

-- select 
-- lt_month
-- , max(case when cohort='2016-01-01' then intensity else null end) as "jan"
-- , max(case when cohort='2016-02-01' then intensity else null end) as "feb"
-- , max(case when cohort='2016-03-01' then intensity else null end) as "mar"
-- , max(case when cohort='2016-04-01' then intensity else null end) as "apr"
-- , max(case when cohort='2016-05-01' then intensity else null end) as "may"
-- , max(case when cohort='2016-06-01' then intensity else null end) as "jun"
-- from cohort_life
-- group by lt_month
-- order by 1

/*
График "Вымирание когорты".
Иллюстрирует изменение доли активных студентов относительно изначального размера когорты (share_active) в течение времени её жизни (lt_month)
*/

select 
lt_month
, max(case when cohort='2016-01-01' then active_students_share else null end) as "jan"
, max(case when cohort='2016-02-01' then active_students_share else null end) as "feb"
, max(case when cohort='2016-03-01' then active_students_share else null end) as "mar"
, max(case when cohort='2016-04-01' then active_students_share else null end) as "apr"
, max(case when cohort='2016-05-01' then active_students_share else null end) as "may"
, max(case when cohort='2016-06-01' then active_students_share else null end) as "jun"
from cohort_life
group by lt_month
order by 1

/*
График "Уроки на 1 активного студента".
Иллюстрирует активность студентов (classes_per_active_student) в течение времени жизни когорты (lt_month)
*/

-- select 
-- lt_month
-- , max(case when cohort='2016-01-01' then intensity_active else null end) as "jan"
-- , max(case when cohort='2016-02-01' then intensity_active else null end) as "feb"
-- , max(case when cohort='2016-03-01' then intensity_active else null end) as "mar"
-- , max(case when cohort='2016-04-01' then intensity_active else null end) as "apr"
-- , max(case when cohort='2016-05-01' then intensity_active else null end) as "may"
-- , max(case when cohort='2016-06-01' then intensity_active else null end) as "jun"
-- from cohort_life
-- group by lt_month
-- order by 1

