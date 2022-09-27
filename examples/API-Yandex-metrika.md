```python
!pip install tapi-yandex-metrika 
#скачиваем библиотеку для работы с Яндекс.Метрикой

!pip install --upgrade tapi-yandex-metrika==2021.5.28

#import sys
#!{sys.executable} -m pip install tapi-yandex-metrika 

from tapi_yandex_metrika import YandexMetrikaStats
import json
import pandas as pd

#после авторизации и создания токена

ACCESS_TOKEN = "AgAEA7qiPkdWAAbJrz98tN5cykRuipQQALE5BRU" # кладем в переменную токен
METRIC_IDS = "69215599" # кладем в переменную токен идентификатор нашего счетчика

#Создаем объект для подключения к данным
api = YandexMetrikaStats( 
                    access_token=ACCESS_TOKEN,     
                    #receive_all_data=True # Если True, будет скачивать все части отчета. По умолчанию False.
)
#Получим из аналитики количество уникальных пользователей и визитов по дням за последние 30 дней.

#Для начала зададим параметры запроса.

params = {
    'ids' : METRIC_IDS, # из какого счетчика забираем данные
    'date1' : "90daysAgo", # с какой даты формируем отчет
    'date2' : "yesterday", # по какую дату формируем отчет
    'sort' : "ym:s:date", # сортировка по дате визита
    'metrics' : "ym:s:users, ym:s:visits", #какие величины запрашиваем
    'dimensions' : "ym:s:date", #какие группировки используем
    'accuracy' : "full", # указываем точность вычисления результата. Позволяет управлять семплированием (количеством визитов, использованных при расчете итогового значения)
    'limit' : 1000 # количество элементов на странице
}
#Выполняем запрос и получаем данные из Yandex.Metrika API

raw_result = api.stats().get(params=params)
#Извлекаем из ответа только нужные данные для отчета, забираем из первого элемента словаря все по значению 'data'
result = raw_result().data
result = result['data']

#Сконвертируем в удобный для нас словарь, для этого распарсим исходный list формата json в dict

#Мы хотим, чтобы в нашем словаре лежали ключи: дата и две заказанные метрики: количество уникальных пользователей в этот день и количество визитов.
dict_data = {} # создаем пустой dict 


for i in range(0, len(result)):
    dict_data[i] = {
            'date':result[i]["dimensions"][0]["name"],
            'users':result[i]["metrics"][0],
            'visits':result[i]["metrics"][1],
          }

#Создаем DataFrame из словаря с помощью метода .from_dict(), в аргументы ему передаем:

#orient='index', то есть что ключи должны быть строками, по умолчанию ключи становятся столбцами.

#columns=dict_keys, то есть колонками должны стать ключи вложенного словаря, которые мы вытащили строчкой выше.

dict_keys = dict_data[0].keys() # выбираем ключи

attendance = pd.DataFrame.from_dict(dict_data, orient='index',columns=dict_keys) #создаем датафрейм
attendance


```
