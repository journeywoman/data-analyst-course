``` python

import pandas as pd
from matplotlib import pyplot as plt
import seaborn as sns
import datetime

# загружаем данные

workouts = pd.read_csv('workouts (2).csv', parse_dates=['start_at'])
users = pd.read_excel('users (2).xlsx')

# сразу создадим новый датафрейм, в котором соединены уроки и пользователи

workouts_users =  pd.merge(workouts, users, 
                     how='left',
                     left_on='client_id', right_on='user_id')
                     
# отфильтровали бесплатные тренировки и сделали копию датафрейма
cost_age_data = workouts_users[['status','cost','age']].loc[workouts_users.workout_schedule_type!='trial'].dropna().copy()

# Изменяем тип данных столбцов age и cost на int. Это нужно, чтобы на подписях к диаграммам числа были целыми
cost_age_data['age'] = cost_age_data['age'].apply(int) 
cost_age_data['cost'] = cost_age_data['cost'].apply(int)

# Кодируем столбец status, присваивая 1 в случаях, если он равен failed_by_client и 0 в случае success
cost_age_data['status'] = cost_age_data.status.map({'success':0,'failed_by_client':1})

# создаём агрегированные наборы данных
cost_age_data_agg = cost_age_data.groupby(['cost','age'])['status'].mean().reset_index() #считаем долю прогулов по возрасту и цене 

# пишем функцию, которая поможет разбить клиентов на возрастные группы

def calculate_age_bins(age):
    if age <= 20:
        return '0-20'
    if age <= 25:
        return '21-25'
    if age <= 30:
        return '26-30'
    if age <= 35:
        return '31-35'
    if age <= 40:
        return '36-40'
    if age <= 45:
        return '41-45'
    if age <= 50:
        return '46-50'
    return '50+'
    
    # кладем в новый столбец возрастную группу
cost_age_data['age_bin'] = cost_age_data.age.apply(calculate_age_bins)

# создаём агрегированный набор данных
cost_age_data_agg = cost_age_data.groupby(['cost','age_bin'])['status'].mean().reset_index()

heat_cost_age_data = cost_age_data_agg.pivot(index="age_bin", 
                                        columns="cost", 
                                        values="status")
# Создаем тепловую карту
plt.figure(figsize=(20,8))
sns.heatmap(heat_cost_age_data, 
            annot=True, 
            cmap="YlGnBu", 
            fmt='g')
plt.title('Зависимость количества прогулов от цен и возраста') 
plt.xlabel('Цена') 
plt.ylabel('Возрастная группа') 


plt.show()
```
