## Использование оконных агрегирующих функций
``` sql
 select *
 from
    (select *
         , cnt_city_tariff::float / cnt_all::float as share_city_tariff -- доля городотарифов от общего числа заказов
         , cnt_city::float / cnt_all::float as share_city -- доля городов от общего числа заказов
    from
        (select id_order
             , order_time
             , name_tariff
             , name_city
             , count (*) over () as cnt_all -- посчитает общее число непустых строк
             , count (*) over (partition by name_city) as cnt_city -- посчитает количество заказов по каждому городу
             , count (*) over (partition by name_city, name_tariff) as cnt_city_tariff --  посчитает количество заказов в городе по определенному тарифу
        from skytaxi.order_list as ol
         left join skytaxi.city_dict as cd on ol.id_city = cd.id_city
         left join skytaxi.tariff_dict as td on ol.id_tariff = td.id_tariff
         ) as t
     ) as c
 where share_city > 0.2 and share_city_tariff < 0.05 -- выводит города с долей больше 20% и маленькой долей тарифа (меньше 5%)
 
 
 
 
 select name_city
      , credit_type
      , amt_loan
      , date_loan
      , sum (amt_loan) over () as all_amt_loan -- общая сумма займов
      , sum (amt_loan) over (partition by name_city) as city_amt_loan -- сумма займов по городам
      , sum (amt_loan) over (partition by credit_type) as credit_type_amt_loan -- сумма займов по credit_type
      , sum (amt_loan) over (partition by name_city,credit_type) as city_credit_type_amt_loan -- сумма займов по credit_type and city
      , sum (amt_loan) over (order by date_loan asc) as all_amt_loan_cum -- общая сумма займов нарастающим итогом
      , sum (amt_loan) over (partition by name_city order by date_loan asc) as city_amt_loan_cum -- сумма займов по городам нарастающим итогом
      , sum (amt_loan) over (partition by credit_type order by date_loan asc) as credit_type_amt_loan_cum -- сумма займов по credit_type нарастающим итогом
      , sum (amt_loan) over (partition by name_city,credit_type order by date_loan asc) as city_credit_type_amt_loan_cum -- сумма займов по credit_type and city нарастающим итогом
      , avg (amt_loan) over () as avg_all -- средняя сумма по всем займам
 from skybank.late_collection_clients as lcc
  left join skybank.region_dict as rd on lcc.id_city = rd.id_city
 order by name_city, date_loan
 
 select name_city
      , credit_type
      , amt_loan
      , date_loan
      , avg (amt_loan) over () as avg_all -- средняя сумма по всем займам
      , avg (amt_loan) over (partition by name_city) as avg_city
      , avg (amt_loan) over (partition by credit_type, name_city) as avg_city_type
      , avg (amt_loan) over (order by date_loan asc) as avg_all
      , avg (amt_loan) over (partition by name_city order by date_loan asc) as avg_city
      , avg (amt_loan) over (partition by credit_type, name_city order by date_loan asc) as avg_city_type
from skybank.late_collection_clients as lcc
  left join skybank.region_dict as rd on lcc.id_city = rd.id_city
 order by  date_loan
      

select t.*
     , avg (sum_amt_loan) over (order by month_loan rows between 2 preceding and current row) as ma_3 
     , avg (sum_amt_loan) over (order by month_loan rows between 6 preceding and current row) as ma_7
     , avg (sum_amt_loan) over (order by month_loan rows between 3 preceding and 3 following) as ma_7_2side
     , avg (sum_amt_loan) over () as avg_all 
from
(select date_trunc ('month', date_loan::date) as month_loan
     , sum (amt_loan) as sum_amt_loan
from skybank.late_collection_clients
group by month_loan
) t
```
