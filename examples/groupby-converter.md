Как изменялась ежемесячная выручка по каждому типу тренировок в течение 2020 года? 
Получи датафрейм с разбивкой выручки по месяцам и по типам тренировок с помощью группировки с суммой методом groupby().
```python
import pandas as pd
from datetime import datetime
payments =  pd.read_excel('payments_hw.xlsx')# загружаем данные по платежам

pd.set_option('display.float_format', lambda x: '%.2f' % x) # эта строка поможет удобно выводить большие числа в этом ноутбуке

payments['payment_date'] = pd.to_datetime(payments['payment_date'])# преобразовываем даты

payments_2020 = payments.loc[(payments['payment_date'] >'2020-01-01')&(payments['payment_date'] <'2020-12-31')]# выбираем данные за 2020 год

USD_RATE = 73.14 # курс доллара

def converter_function(currency): # определили функцию для заполнения столбца fx_rate методом .apply()
    if currency =='USD':
        return USD_RATE
    else:
        return 1
        
payments_2020['fx_rate'] = payments_2020['currency'].apply(converter_function) # создали колонку для подсчета суммы по курсу

payments_2020['amount_rub'] = payments_2020['amount'] * payments_2020['fx_rate'] # создали колонку с суммой платежа в рублях

payments_2020['amount_rub'].groupby([payments_2020['payment_date'].dt.month,payments_2020['workout_type']]).sum() # сгруппировали по месяцам и типам тренеровок для подсчета выручки
```
