## Оконные функции row_number, rank, dense_rank, lag, lead
``` sql
-- Победители в каждой песочнице:

select *
from
(
select id_order
     , order_time
     , name_city
     , name_tariff
     , row_number () over (order by order_time asc) as rn
     , row_number () over (partition by name_city  order by order_time asc) as rn_city
     , row_number () over (partition by name_city, name_tariff order by order_time asc) as rn_city_tarriff
from skytaxi.order_list l
 left join skytaxi.city_dict cd 
  on l.id_city = cd.id_city
 left join skytaxi.tariff_dict td 
  on l.id_tariff = td.id_tariff
  ) t 
where rn = 1 or rn_city = 1 or rn_city_tarriff = 1



select id_client
     , amt_loan
     , date_loan
     , row_number () over (order by amt_loan desc) as rn
     , rank () over (order by amt_loan desc) as "rank"
     , dense_rank () over (order by amt_loan desc) as "d_rank"
from skybank.late_collection_clients

-- Для определения промежутка времени между событиями:

select id_order
     , order_time
     , lag (order_time) over (order by order_time asc) as order_time_prev
     , lead (order_time) over (order by order_time asc) as order_time_next
from skytaxi.order_list


-- Для создания отедльных песочниц по городам:

select id_order
     , order_time
     , name_city
     , lag (order_time) over (partition by name_city order by order_time asc) as order_time_prev
     , lead (order_time) over (partition by name_city order by order_time asc) as order_time_next
from skytaxi.order_list as ol 
 left join skytaxi.city_dict as cd on ol.id_city = cd.id_city
 
--  Для притягивания значения, например, через одно:
 select id_order
     , order_time
     , lag (order_time,2) over ( order by order_time asc) as order_time_prev
     , lead (order_time,2) over ( order by order_time asc) as order_time_next
from skytaxi.order_list as ol 
 left join skytaxi.city_dict as cd on ol.id_city = cd.id_city
 
 
--  Находим разницу между заказами в каждом городе:
 select name_city
      , avg (delta_time)
 from 
     (
     select id_order
          , order_time
          , name_city
          , order_time - lag (order_time) over ( partition by name_city order by order_time asc) as delta_time
    from skytaxi.order_list as ol 
     left join skytaxi.city_dict as cd on ol.id_city = cd.id_city
     ) as t
group by name_city

-- ИЛИ 

select name_city
      , avg (delta_time)
 from 
     (
     select id_order
          , order_time
          , name_city
          , lead (order_time) over ( partition by name_city order by order_time asc) - order_time as delta_time
    from skytaxi.order_list as ol 
     left join skytaxi.city_dict as cd on ol.id_city = cd.id_city
     ) as t
group by name_city

-- Задача 1
-- Какое место по времени создания занимает заказ номер 110551

-- Среди всех заказов?
-- Среди заказов своего города?
-- Среди заказов своего тарифа?
-- Среди заказов своего города и своего тарифа?


select t.*
from 
(
select id_order
     , row_number () over (order by order_time) as all_orders
     , name_city
     , row_number () over (partition by name_city order by order_time) as city_number
     , name_tariff
     , row_number () over (partition by name_tariff order by order_time) as tariff_namber
     , row_number () over (partition by name_tariff,name_city  order by order_time) as city_tariff_number
from skytaxi.order_list as ol 
 left join skytaxi.city_dict as cd on ol.id_city = cd.id_city
     
 left join skytaxi.tariff_dict as td 
   on ol.id_tariff = td.id_tariff
   ) as t
where id_order = 110551

select *
from (select  t.*
              , name_tariff
              , name_city
              , row_number() over (order by order_time) as rn_all
              , row_number() over (partition by name_city order by order_time) as rn_city
              , row_number() over (partition by name_tariff order by order_time) as rn_tariff
              , row_number() over (partition by name_city, name_tariff order by order_time) as rn_city_tariff
      from skytaxi.order_list t
          left join skytaxi.city_dict a
              on t.id_city = a.id_city
          left join skytaxi.tariff_dict b
              on t.id_tariff = b.id_tariff
    ) t
where id_order = 110551

```
