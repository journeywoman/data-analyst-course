```python
import pandas as pd
df = pd.read_csv('genotype.csv')

df.agg(["count", "min", "max", "median", "mean", "skew"])

import matplotlib.pyplot as plt
df.boxplot(column=['A', 'B'], grid=False)
plt.show()

import seaborn as sns

sns.set(rc={'figure.figsize':(11.7,8.27)})
ax1 = sns.histplot(df['A'], color='blue')
ax2 = sns.histplot(df['B'], color='orange')
ax1.set(xlabel='Урожайность', ylabel='Кол-во растений')
plt.title('Урожайность генотипов')

#Альтернативный способ отображения гистограмм через matplotlibАльтернативный способ отображения гистограмм через matplotlib
#import matplotlib.pyplot as plt
#fig, (ax1, ax2) = plt.subplots(1, 2)
#fig.suptitle('Frequency histogram of genotypes yield')
#ax1.hist(df['A'], bins=10, histtype='bar', ec='k') 
#ax2.hist(df['B'], bins=10, histtype='bar', ec='k') 
#ax1.set_xlabel("Yield")
#ax2.set_xlabel("Yield")
#plt.show()


import scipy.stats as stats

#Используем тест Шапиро: его -p_value говорит нам о том, нормально ли наше распределение
w, pvalue = stats.shapiro(df['A'])
w, pvalue

w, pvalue = stats.shapiro(df['B'])
w, pvalue

# Критерий Манна-Уитни
# perform two-sided test. You can use 'greater' or 'less' for one-sided test
stats.mannwhitneyu(x=df['A'], y=df['B'], alternative = 'two-sided')

stats.mannwhitneyu(x=df['A'], y=df['B'], alternative = 'greater')
# output

```
