```python
##### Для clickhouse
#!pip install clickhouse_driver - можно закрузить одним из двух способов

import sys
!{sys.executable} -m pip install clickhouse_driver 

from clickhouse_secret import ch_secret # Открываем файл с информацией о данных для доступа

from clickhouse_driver import Client # импортируем нужную функцию

client = Client(ch_secret['db_host'],
                port=ch_secret['db_port'],
                user=ch_secret['db_user'],
                password=ch_secret['db_password'],
                verify=False,
                database=ch_secret['db_name']
                )
result, columns = client.execute('SELECT * FROM events', 
                                 with_column_types=True)
col = []    # Аналогично создаем датафрейм и здесь                
for desc in columns:
    col.append(desc[0])
events = pd.DataFrame(result, columns=col)
events.head()

### Ещё один способ получить данные из базы данных напрямую в pandas — использовать метод .read_sql().

#import sys
#!{sys.executable} -m pip install clickhouse-sqlalchemy 

!pip install clickhouse-sqlalchemy # установим расширение sqlalchemy для clickhouse

from sqlalchemy import create_engine

engine_path = f"""clickhouse+native://{ch_secret['db_user']}:{ch_secret['db_password']}@{ch_secret['db_host']}:{ch_secret['db_port']}/{ch_secret['db_name']}"""
## Создадим объект подключения к БД, для этого нам нужно данные подключения передать в таком виде:
## для clickouse запрос выглядит так: clickhouse+native://user:password@host:port/db_name
## для postgreSQL еще проще: postgresql://user:password@host:port/db_name

engine = create_engine(engine_path) # драйвер создаем 
query = 'SELECT * FROM events'
events = pd.read_sql(query, engine)
events 

```
