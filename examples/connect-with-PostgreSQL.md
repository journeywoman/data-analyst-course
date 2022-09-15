```python
from postgresql_secret import pg_secret # Подключаемся к файлу, в котором храняться доступы

!pip install psycopg2-binary # скачивает уже собранную библиотеку


#!pip install psycopg2 # само собирает библиотеку из исходников
#import sys
#!{sys.executable} -m pip install psycopg2 

import psycopg2 # импортируем библиотеку

connection = psycopg2.connect(
                             database=pg_secret['db_name'],
                             user=pg_secret['db_user'],
                             password=pg_secret['db_password'],
                             host=pg_secret['db_host'],
                             port=pg_secret['db_port'],
                             )
cursor = connection.cursor() # Для создания запроса нам нужно настроить cursor — объект, который занимается выполнением SQL и выборкой данных. Применим cursor к нашему соединению и положим результат в отдельную переменную, чтобы код легче читался.
query = "SELECT * FROM users"    # Составим запрос к БД
cursor.execute(query)            # Сделаем запрос
result = cursor.fetchall()  # Запросим все строки, мы получим результат в виде списка кортежей
cursor.description # Посмотрим на описание данных с помощью метода .description, там должны быть спрятаны имена колонок.
columns = []                     # В этот список будем сохранять названия колонок
for desc in cursor.description:  
    columns.append(desc[0])      # Названия колонок возьмем из первого элемента cursor.description

    
users = pd.DataFrame(result, columns=columns)  # Из списка кортежей сделаем датафрейм
users.head()

```
