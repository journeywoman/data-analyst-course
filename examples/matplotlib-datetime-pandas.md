##### Постройте визуализацию: для каждого месяца покупки по левой оси постройте сумму оплат, а по правой - количество оплат
```python

import pandas as pd
import datetime as dt
from matplotlib import pyplot as plt

Purchase_List = pd.read_csv('Purchase_List.csv')

Purchase_List['date_purchase']=pd.to_datetime(Purchase_List['date_purchase'])
Purchase_List['month_purchase']=Purchase_List['date_purchase'].dt.month
Purchase_List.head()

Month_purchase=Purchase_List.groupby('month_purchase').agg({'id_purchase':'count','amt_pay':'sum'}).reset_index()
Month_purchase
figure = plt.figure(figsize=(10, 10)) 
ax1 = figure.add_subplot(111)

plt.title('Оплаты по месяцам') 
plt.xlabel('Месяц') 
plt.ylabel('Сумма оплат') 

plt.grid() 

ax1.plot(Month_purchase['month_purchase'],Month_purchase['amt_pay'], color='g', label = 'Сумма оплат')
ax1.legend(loc='upper right')

ax2=ax1.twinx()
ax2.bar(Month_purchase['month_purchase'],Month_purchase['id_purchase'],
        color='c',
        alpha = 0.5,
        label = 'Количество оплат')
ax2.legend(loc='upper center')
plt.show()
```
