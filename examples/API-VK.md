```python
import requests
import pandas as pd
import json

def search_query(q, sorting=0): # Если не передать параметр sorting, то по умолчанию он будет равен 0

    #Параметры sort
    #0 — сортировать по умолчанию (аналогично результатам поиска в полной версии сайта);
    #1 — сортировать по скорости роста;
    #2 — сортировать по отношению дневной посещаемости к количеству пользователей;
    #3 — сортировать по отношению количества лайков к количеству пользователей;
    #4 — сортировать по отношению количества комментариев к количеству пользователей;
    #5 — сортировать по отношению количества записей в обсуждениях к количеству пользователей.
    params = {
        'q': q,                 # Поисковой запрос
        'access_token': token,
        'v':'5.89',
        'sort': sorting,
        'count': 100           # Просим отдать нам 100 записей, это максимум для обычного запроса
    }
    req = requests.get('https://api.vk.com/method/groups.search', params).json()
    req = req['response']['items']
    return req


python_communities = search_query('python', 2)
python_communities

group_df = pd.DataFrame(python_communities)
group_df
#Найдем количество подписчиков для каждой группы
groups_ids = ''  # сформируем строку с ID групп через запятую

for x in group_df['id']:
    groups_ids += str(x) # Преобразуем ID из числа в строку
    groups_ids += ', '

url_data = 'https://api.vk.com/method/groups.getById'

params = {
    'access_token': token, # токен
    'v':'5.89', # версия
    'group_ids': groups_ids, # список ID групп
    'fields':  'members_count,description' # здесь мы передали дополнительные параметры групп
}

req = requests.get(url_data, params) # сформировали запрос
res = req.json()['response']

groups_members_df = pd.DataFrame(res).sort_values('members_count', ascending=False) 
groups_members_df.head()

#Получим количество постов в каждой группе

url_data = 'https://api.vk.com/method/wall.get'

params = {'access_token': token,
          'v':'5.89',
          'owner_id': '-' + str(3183750),   # Таким интересным способом согласно документации передается ID группы
          'filter': 'all'  # Берём все посты

        }
req = requests.get(url_data, params)
res = req.json()

import time # чтобы спать

post_counts = []  # В этот список будем последовательно записывать информацию о количестве записей в каждой из групп

for group_id in groups_members_df['id']:

    url_data = 'https://api.vk.com/method/wall.get'

    params = {'access_token': token,
          'v':'5.89',
          'owner_id': '-' + str(group_id),   # Таким интересным способом согласно документации передается ID группы
          'filter': 'all'  # Берем все посты
            }
    req = requests.get(url_data, params)
    res = req.json()
    
    try: # пробуем выполнить код
        count = res['response']['count'] # присваиваем значение ключа 'count' из словаря 'response', полученным в ответе на запрос
        post_counts.append(count) # добавляем значение в список с количеством записей
    except: # если вылетает ошибка, делаем то, что в except — присваиваем значение ноль
        post_counts.append(0)
    
    time.sleep(0.5) # Ограничиваем частоту запросов до одного запроса в полсекунды
    
groups_members_df = groups_members_df[['id', 'name', 'screen_name', 'members_count', 'description']] 
groups_members_df['posts'] = post_counts
groups_members_df

#Мы хотим запустить таргетированную рекламу онлайн-уроков по английскому языку для детей на 10 городов РФ. 
#Чтобы выбрать эти города, нам нужно знать, где живет наша целевая аудитория. 
#Найдем 10 самых частотных городов проживания участников сообщества Skysmart.
#Нам нужно получить данные всех участников сообщества. Для этого есть метод groups.getMembers
#С параметром 'count' можем получить максимум 1000

# pd.set_option('max_colwidth', None)  
# Если хотите видеть всё содержимое ячеек в датафрейме, выполните эту команду с настройкой


offset = 0
max_count = 1000
members = pd.DataFrame()

while True:      # Повторять цикл пока его не остановят по условию ниже
    
    url = 'https://api.vk.com/method/groups.getMembers'
    params = {
        'group_id': 'skysmart',
        'access_token': token,
        'v':'5.89',
        'fields': 'city',
        'count': max_count,
        'offset': offset
    }
    
    req = requests.get(url=url, 
                       params=params)
    res = req.json()
    
    if len(res['response']['items']) != 0:      # Проверяем вернулись ли нам хоть какие-нибудь записи
        members = pd.concat([members, pd.DataFrame(res['response']['items'])]) 
    else:
        break                                   # Если нам ничего не вернулось, то прервать цикл
    print('Уже скачано', offset, 'участников')
    offset = offset + max_count                 # Смещаем offset на число возвращенных записей
    time.sleep(0.5)                             # Опять контролируем частоту запросов, чтобы нас не ограничили

print('Кол-во участников:', len(members))

cities =  members.city.dropna().apply(lambda x: x.get('title', None)).reset_index()

cities.groupby('city').count().sort_values('index', ascending = False).head(10)


```
