## характеристики оплат в разрезе кластеров географии
Посчитайте характеристики оплат в разрезе кластеров географии  в 2016 году.
-- Нас интересуют операции типа “**Покупка уроков**” или **“Начисление корпоративному клиенту**” со статусом платежа = **success** (успешная транзакция).
-- Какие показатели нас интересуют?**
-- - количество оплат (**`payments`)**
-- - количество оплативших студентов (**`students`**)
-- - количество оплат на 1 студента (**`payments_per_student`**)
-- - общая сумма оплат (**`total_amount`**)
-- - средняя сумма одной оплаты (**`avg_amount`**)
-- - количество купленных уроков (**`classes_purchased`**)
-- - средняя цена урока (**`avg_class_price`**)
-- - среднее количество уроков в одном платеже (**`avg_classes_in_purchase`**)

``` sql
select s.geo_cluster
     , count (p.id_transaction) as payments
     , count (distinct p.user_id) as students
     , count (p.id_transaction) / count (distinct p.user_id) as payments_per_student
     , sum (p.payment_amount) as total_amount
     , sum (p.payment_amount) / count (p.id_transaction) as avg_amount
     , sum (p.classes) as classes_purchased
     , sum (p.payment_amount) / sum (classes) as avg_class_price
     , avg (p.classes) as avg_classes_in_purchase
from skyeng_db.payments as p
left join skyeng_db.students as s on p.user_id = s.user_id
where operation_name in ('Покупка уроков', 'Начисление корпоративному клиенту')
  and status_name = 'success'
  and date_trunc ('year', transaction_datetime) = '2016-01-01'
group by s.geo_cluster
order by payments desc

```
