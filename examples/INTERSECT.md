## Найдите производителей, которые производили бы как ПК
со скоростью не менее 750 МГц, так и ПК-блокноты со скоростью не менее 750 МГц.
Вывести: Maker
```sql
select maker
from
(select model
from PC
where speed >= 750) as PC
join Product on PC.model = Product.model
INTERSECT
select maker
from
(select model
from Laptop
where speed >= 750) as L
join Product on L.model = Product.model
```
