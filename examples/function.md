```python
staff = { #  словарь со списком сотрудников компании и их должностями
    'Ирина':'Customer Support',
    'Иван':'Customer Support',
    'Дмитрий':'Marketing',
    'Ольга':'CPO',
    'Анастасия':'Sales',
    'Александр':'Sales',
}

metrics = { #  словарь со списком ключевых показателей бизнеса, которые необходимы и доступны для каждой должности
    'Customer Support': ['Retention', 'MAU',],
    'Sales': ['New Users',],
    'Marketing': ['New Orders',],
    'CPO': ['Retention', 'MAU','New Users','New Orders', 'C1'], # как видите, директор по продукту следит за всеми метриками!  
    'Intern': ['Retention', 'New Users','New Orders']
}


data = { #  словарь с заранее подготовленными значениями метрик по месяцам, которые мы будем использовать в ежемесячных отчетах
    '2020-01-01':{
        'MAU':12300, # общее количество уникальных пользователей за месяц, которые воспользовались нашим сервисом
        'Retention':0.88, # доля от общего количества пользователей прошлого месяца, которая вернулась в наш сервис в текущем месяце
        'New Users':9012, # количество новых пользователей в нашем сервисе
        'New Orders': 1404 # количество новых заказов от новых пользователей нашего сервиса
    },
    '2020-02-01':{'MAU':11432,'Retention':0.75, 'New Users':12011, 'New Orders':2443 },
    '2020-03-01':{'MAU':13348,'Retention':0.89, 'New Users':8732, 'New Orders':1454 },
    '2020-04-01':{'MAU':13400,'Retention':0.93, 'New Users':9342, 'New Orders':2235 },
    '2020-05-01':{'MAU':13354,'Retention':0.79, 'New Users':11032, 'New Orders':4402 }
}

data2 = data
for date, info in data.items():
    C1= info['New Orders']/info['New Users']
    data2[date]['C1'] = C1

from datetime import datetime

def date_function(x):
    request_date = datetime.strptime(x,"%Y-%m-%d")
    new_date = request_date.strftime("%d.%m.%Y")
    return new_date
def metrics_function(x):
    new_data={}
    for day,info in data2.items():
        y = date_function(day)
        new_data[y] = info
    result = new_data.get(x,'Нет данных')
    return result
    
#x - имя
def metrics_name_funtion(x):
    position = staff.get(x)
    need_metrics = metrics.get(position)
    return need_metrics
    
#x - имя, y - дата
def all_metrics_function(x,y):
    list_need_metrics = metrics_name_funtion(x) # получаем список нужных метрик для этого сотрудника
    dict_day_metrics = metrics_function(y) # получаем все метрики на определенную дату
    need_metrics={}
    for i in list_need_metrics:
        info = dict_day_metrics.get(i)
        need_metrics[i]= info
    return need_metrics

date = '01.02.2020'
name_list = []
for n, p in staff.items():
    name_list.append(n)
for i in name_list:
    print(f'Добрый день, {i}! Для тебя есть интересные данные за {date}: {all_metrics_function(i, date)}')
```
