```python
import pandas as pd

df = pd.read_csv('Taxi_AB_test.csv', sep=';')
df_gr = df.groupby(['flag_group','id_city']).agg({'flag_conv':'sum','user_id':'count'}).reset_index()

df_gr['conv'] = df_gr['flag_conv'] / df_gr['user_id']

df_gr.sort_values(by = 'id_city') # сортируем по городам,чтобы легче было посмотреть полученные результаты

from scipy.stats import ttest_ind

s, p = ttest_ind(df[df['flag_group']=='control']['flag_conv'], df[df['flag_group']=='pilot']['flag_conv']) # считаем общий результат по всей выборке

# считаем в разбивке по городам
for i in df['id_city'].unique():
  print(i)
  s, p = ttest_ind(df[(df['id_city']==i) & (df['flag_group']=='control')]['flag_conv'], df[(df['id_city']==i) & (df['flag_group']=='pilot')]['flag_conv'])
  print(p)
  print()
# По итогом расчетов видно, что p_value < 0,05 только в Питере, можно еще посмотреть на МСК, остальные города существенной разницы между контрольной и тестовой группой не показали




```
