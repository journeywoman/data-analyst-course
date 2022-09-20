```sql
--Код, который в сгруппированной (для каждого города и тарифа - каунт строк) таблице 
--создаст 3 дополнительных колонки, которые заполнит топ-1, топ-2 и топ-3 тарифом в рамках города по кол-ву заказов
select distinct name_city
     , NTH_VALUE(name_tariff,1) OVER (PARTITION BY name_city ORDER BY cn desc) AS "Top_1_tariff"
     , NTH_VALUE(name_tariff,2) OVER (PARTITION BY name_city ORDER BY cn desc) AS "Top_2_tariff"
     , NTH_VALUE(name_tariff,3) OVER (PARTITION BY name_city ORDER BY cn desc) AS "Top_3_tariff"
from
(select name_city
     , name_tariff
     , count(*) as cn
from skytaxi.order_list as ol 
left join skytaxi.tariff_dict td on ol.id_tariff = td.id_tariff
left join skytaxi.city_dict cd on ol.id_city = cd.id_city
group by name_city, name_tariff) as d

```
