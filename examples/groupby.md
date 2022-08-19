```python
import pandas as pd

client_pool = pd.read_csv('client_pool.csv')
city_dict = pd.read_csv('city_dict.csv')

########  1. Постройте группировку: для каждого города найдите количество клиентов. 
################   Сохраните результат в датафрейм client_pool_city
client_pool_cities = client_pool.groupby('id_city')['id_client'].count()
client_pool_city = client_pool_city.reset_index()
print(client_pool_city)
########  2. Постройте группировку: для каждого пола найдите количество клиентов
################   Сохраните результат в датафрейм client_pool_gender
client_pool_gender = client_pool.groupby('gender')['id_client'].count()
client_pool_city_genders = client_pool.groupby(['id_city', 'gender'])['id_client'].count()
client_pool_city_gender = client_pool_city_genders.reset_index()
print(client_pool_city_gender)
########  3. Постройте группировку: найдите количество клиентов для каждой комбинации пол-город
################   Сохраните результат в датафрейм client_pool_city_gender########  1. Постройте группировку: для каждого города найдите количество клиентов. 
################   Сохраните результат в датафрейм client_pool_city
client_pool_cities = client_pool.groupby('id_city')['id_client'].count()
client_pool_city = client_pool_city.reset_index()
print(client_pool_city)
########  2. Постройте группировку: для каждого пола найдите количество клиентов
################   Сохраните результат в датафрейм client_pool_gender
client_pool_gender = client_pool.groupby('gender')['id_client'].count()
client_pool_city_genders = client_pool.groupby(['id_city', 'gender'])['id_client'].count()
client_pool_city_gender = client_pool_city_genders.reset_index()
print(client_pool_city_gender)
########  3. Постройте группировку: найдите количество клиентов для каждой комбинации пол-город
################   Сохраните результат в датафрейм client_pool_city_gender
#######  С помощью функции merge присоедините таблицу city_dict к таблице client_pool_city,
#чтобы в ней была расшифровка городов
client_pool_city_f = pd.merge(client_pool_city, city_dict, on='id_city')
client_pool_city_f.drop(columns=['id_city'])

#######  Результат запишите в датафрейм client_pool_city_f
#######  В результирующем датафрейме client_pool_city_f не должно быть колонки id_city
client_pool_city_gender_f = pd.merge(client_pool_city_gender, city_dict, how = 'left', on='id_city')
client_pool_city_gender_f_2 = client_pool_city_gender_f.drop(columns=['id_city'])
#######  С помощью функции merge присоедините таблицу city_dict к таблице client_pool_city_gender,
## чтобы в ней была расшифровка городов
#######  Результат запишите в датафрейм client_pool_city_gender_f. 
#######  В результирующем датафрейме client_pool_city_gender_f не должно быть колонки id_city
client_pool_city_gender_f_women = client_pool_city_gender_f_2.loc[client_pool_city_gender_f['gender'] == 'F']

#######  Создайте новый датафрейм client_pool_city_gender_f_women, в который запишите 
#######  строчки датафрейма client_pool_city_gender_f, соответствующие женскому полу (с помощью слайсования датафрейма)
#######  Переименуйте поле id_client в client_pool_city_gender_f_women на "cnt_women" 
client_pool_city_gender_f_women.rename(columns = {'id_client':'cnt_women'}, inplace = True)
client_pool_city_gender_f_women
client_pool_city_gender_f_men = client_pool_city_gender_f_2.loc[client_pool_city_gender_f['gender'] == 'M']
client_pool_city_gender_f_men.rename(columns = {'id_client':'cnt_men'}, inplace = True)
client_pool_city_gender_f_men
#######  Создайте новый датафрейм client_pool_city_gender_f_men, в который запишите 
#######  строчки датафрейма client_pool_city_gender_f, соответствующие мужскому полу (с помощью слайсования датафрейма)
#######  Переименуйте поле id_client в client_pool_city_gender_f_men на "cnt_men" 
client_pool_city_gender_gencol = pd.merge(client_pool_city_gender_f_women, client_pool_city_gender_f_men, on='name_city')

#######   Соедините датафреймы client_pool_city_gender_f_men и client_pool_city_gender_f_women с помощью метода merge.
#######   Результат запишите в новый датафрейм client_pool_city_gender_gencol
client_pool_city_gender_gencol['cnt_all'] = client_pool_city_gender_gencol['cnt_women'] + client_pool_city_gender_gencol['cnt_men']

#######   Добавьте в результирующий датафрейм client_pool_city_gender_gencol колонку cnt_all,
#которая покажет суммарное количество людей в городе
client_pool_city_gender_gencol.drop(columns=['gender_x', 'gender_y'], inplace=True)
client_pool_city_gender_gencol_new = client_pool_city_gender_gencol.reindex(columns=['name_city','cnt_men','cnt_women', 'cnt_all'])
client_pool_city_gender_gencol_new
#######   В результирующем датафрейме client_pool_city_gender_gencol должны быть колонки ['name_city','cnt_men','cnt_women', 'cnt_all']

client_pool_city_gender_gencol_new['share_men'] = client_pool_city_gender_gencol_new['cnt_men'] / client_pool_city_gender_gencol_new['cnt_all']
client_pool_city_gender_gencol_new['share_women'] = client_pool_city_gender_gencol_new['cnt_women'] / client_pool_city_gender_gencol_new['cnt_all']

#######  Добавьте в датафрейм колонки "share_men" и "share_women", которые будут показывать долю мужчин и женщин для каждого города
client_pool_city_gender_gencol_men = client_pool_city_gender_gencol_new.sort_values(by='share_men', ascending=False)
print(client_pool_city_gender_gencol_men.head(1))

#######  Выведите строчку датафрейма, которая соответствует наибольшей доле мужчин
client_pool_city_gender_gencol_women = client_pool_city_gender_gencol_new.sort_values(by='share_women', ascending=False)
print(client_pool_city_gender_gencol_women.head(1))

#######  Выведите строчку датафрейма, которая соответствует наибольшей доле женщин
Постарайтесь выполнить каждое из этих вычислений одной строчкой кода
#######   1. Выведите долю клиентов, которые приходятся на Москву и Санкт-Петербург (можно воспользоваться функцией isin)
#######   2. Выведите долю мужчин, которые приходятся на Мурманск среди всех мужчин
#######   3. Выведите отношение количества женщин в Москве к количеству мужчин в Сахалинске
#######   4. Выведите суммарное количество женщин в Тюмени и мужчин в Екатеринбурге
client_pool_city_gender_gencol_new[client_pool_city_gender_gencol_new['name_city'].isin(['Moscow','Saint-Petersbourg'])]['cnt_all'].sum() / client_pool_city_gender_gencol_new['cnt_all'].sum()

client_pool_city_gender_gencol_new[client_pool_city_gender_gencol_new['name_city'] == 'Murmansk']['cnt_men'].sum() / client_pool_city_gender_gencol_new['cnt_men'].sum()

client_pool_city_gender_gencol_new[client_pool_city_gender_gencol_new['name_city'] == 'Moscow']['cnt_women'].sum() / client_pool_city_gender_gencol_new[client_pool_city_gender_gencol_new['name_city'] == 'Sakhalinsk']['cnt_men'].sum()

client_pool_city_gender_gencol_new[client_pool_city_gender_gencol_new['name_city'] == 'Tumen']['cnt_women'].sum() + client_pool_city_gender_gencol_new[client_pool_city_gender_gencol_new['name_city'] == 'Ekaterinburg']['cnt_men'].sum()

client_pool_city_gender_gencol_new['difference'] = client_pool_city_gender_gencol_new['cnt_men'] - client_pool_city_gender_gencol_new['cnt_women']
client_pool_city_gender_gencol_new


```
