```python
# Хеширование - если в компании id_user выдается рандомно, то достаточно id_user % 10 и определить в зависимости от остатка в первую или вторую группу.
# Однако, если имеется какая-то логика при выдаче ID, нужно делать хэширование через crc32
import binascii

your_id = 3125678545
hash_your_id = binascii.crc32(bytes(str(your_id),'utf-8'))
hash_your_id % 10

# Соль эксперимента - если применяем в компании больше 1 АБ теста одновременно, то нужно проводить хэширование с использованием "соли"

your_salted_id = str(your_id)+'SaltySalty'
hash_your_salted_id = binascii.crc32(bytes(your_salted_id ,'utf-8'))
hash_your_salted_id % 10

# Считаем результаты

import pandas as pd

df = pd.read_csv('Design_AB_test.csv', sep=';')

df_gr = df.groupby('group').agg({'flag_success':'sum','id_client':'count'}).reset_index() # делаем сводную таблицу по каждой группе
df_gr['conv'] = df_gr['flag_success']/df_gr['id_client'] # считаем конверсию для каждой группы

from scipy.stats import ttest_ind

s, p = ttest_ind(df[df['group']=='control']['flag_success'], df[df['group']=='test']['flag_success']) 
# слайсируем и проводим тест. Смотрим на результаты теста: 

s # если в приделах от -1,96 до 1,96 находится s, то тестовая группа не отличается от контрольной.
#В данном случае тестовая группа показала лучший результат, но взяты грязные данные
p #очень меленькое значение, показывает, что тестовая группа лучше в общем

df_aut = df[df['flag_auth']==1] 
#Возьмем пользователей, которые зарегистрированы, чтобы отсечь тех, кто мог несколько раз заходить на сайт
s, p = ttest_ind(df_aut[df_aut['group']=='control']['flag_success'], df_aut[df_aut['group']=='test']['flag_success'])
s # результат в доверительном интервале, следовательно тестовая группа не отличается существенно от контрольной

```