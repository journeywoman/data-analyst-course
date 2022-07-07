## Использование в запросе нескольких источников записей - декартово произведение. Задача: Найдите пары моделей PC, имеющих одинаковые скорость и RAM. 
В результате каждая пара указывается только один раз, т.е. (i,j), но не (j,i),
Порядок вывода: модель с большим номером, модель с меньшим номером, скорость и RAM.
```sql
Select  distinct ( case when PC1.model > PC2.model then PC1.model else PC2.model end)
     , (case when PC1.model < PC2.model then PC1.model else PC2.model end)
     , PC1.speed, PC1.ram
from PC as PC1, PC as PC2 
Where PC1.speed = PC2.speed
  and PC1.RAM = PC2.RAM 
  and PC1.model != PC2.model
  ```
