```python
## Определи, какой тренер в сети провел больше всего платных занятий за все время. 
## С самыми эффективными сотрудниками мы организуем интервью, чтобы понять, что влияет на их результат.
import pandas as pd

workouts =  pd.read_csv('workouts_hw.csv')# загружаем данные по тренировкам

pd.set_option('display.float_format', lambda x: '%.2f' % x) # эта строка поможет удобно выводить большие числа в этом ноутбуке

non_sales_trainers=workouts.loc[workouts['trainer_department'] != 'Sales']#без работников деппартамента Sales
trainers = non_sales_trainers.groupby('trainer_id')['workout_id'].count()
trainers = trainers.reset_index()

max_trainings = trainers['workout_id'].max() #Нашли максимальное количество тренировок 
trainer = trainers.loc[trainers['workout_id'] == max_trainings]
trainer.drop(columns=['workout_id'])
```
