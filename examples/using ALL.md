## Найдите модели ПК-блокнотов, скорость которых меньше скорости каждого из ПК.
Вывести: type, model, speed
``` sql
select distinct type, laptop.model, laptop.speed
from laptop
join product on laptop.model = product.model
where speed < all (select speed from PC) -- меньше скорости каждого из ПК
```
