## Использование в работе временных таблиц
``` sql
create local temp table user_3payments as -- создали временную таблицу с пользователями, имеющими 3 и более оплаты
select user_id
from skyeng_db.payments
where id_transaction is not null
 and status_name = 'success'
group by user_id
having count (id_transaction) >= 3


select *
from user_3payments

create local temp table user_2pay as -- создали временную таблицу с пользователями, имеющими 2 и более оплаты
select user_id
from skyeng_db.payments
where id_transaction is not null
 and status_name = 'success'
group by user_id
having count (id_transaction) >= 2

select *
from user_2pay


create  table payment_sex_vlasova as -- создаем таблицу с количеством плательщиков в разбивке по полу
select s.student_sex -- считаем число женщин и мужчин, оплативших 3 и более раза
     , count (s.user_id) as count_students
from user_3payments as u3p
join skyeng_db.students s on u3p.user_id = s.user_id
where s.student_sex is not null
group by s.student_sex
union all -- соединяем в одну таблицу
select s.student_sex -- считаем число женщин и мужчин, оплативших 2 и более раза
     , count (s.user_id) as count_students
from user_2pay as u2p
join skyeng_db.students s on u2p.user_id = s.user_id
where s.student_sex is not null
group by s.student_sex

select *
from payment_sex_vlasova 

select 'm' as sex, min (count_students::float)/max (count_students::float) as conversion --  мужчин из 2 оплаты перешли в 3 оплату
from payment_sex_vlasova 
where student_sex = 'm'
union all
select 'f' as sex, min (count_students::float)/max (count_students::float) as conversion --  женщин из 2 оплаты перешли в 3 оплату
from payment_sex_vlasova 
where student_sex = 'f'
```
