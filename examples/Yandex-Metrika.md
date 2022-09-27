```python
#Выясним, из каких каналов маркетинга на нашу посадочную страницу приходят пользователи.

params = dict(
    ids = METRIC_IDS, # из какого счетчика забираем данные
    metrics = "ym:s:users,ym:s:visits,ym:s:pageviews,ym:s:bounceRate,ym:s:avgVisitDurationSeconds",
    dimensions = "ym:s:date,ym:s:<attribution>TrafficSource,ym:s:<attribution>SourceEngine,ym:s:gender,ym:s:ageInterval",
    date1 = "300daysAgo",
    date2 = "yesterday",
    sort = "ym:s:date",
    accuracy="full",
    limit = 600
)
# Аналогично получаем данные из Yandex.Metrika API
result = api.stats().get(params=params)
result = result().data
result = result[0]['data']

dict_data = {} # Снова создаем пустой словарь данных

#Парсим исходный json в словарь
for i in range(0, len(result)-1):
    dict_data[i] = {
            'date':result[i]["dimensions"][0]["name"],
            'traffic-source':result[i]["dimensions"][1]["name"],
            'traffic-details':result[i]["dimensions"][2]["name"],
            'gender':result[i]["dimensions"][3]["name"],
            'age':result[i]["dimensions"][4]["name"],
            'users':result[i]["metrics"][0],
            'visits':result[i]["metrics"][1],
            'pageviews':result[i]["metrics"][2],
            'bounceRate':result[i]["metrics"][3],
            'avgVisitDurationSeconds':result[i]["metrics"][4]
          }

    
#Создаем DataFrame из словаря 
dict_keys = dict_data[0].keys()
df = pd.DataFrame.from_dict(dict_data, orient='index',columns=dict_keys)

#С помощью set() получим только уникальные источники трафика:
marketing_channels = set(df['traffic-source'])





```
