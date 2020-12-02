---
title: Визуализация данных с помощью Apache Spark
description: Создание разнообразных визуализаций данных с помощью Apache Spark и записных книжек Azure синапсе Analytics
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 10/20/2020
ms.author: midesa
ms.openlocfilehash: 8735514b639cb0322a83ffb19d661027327c0f73
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458769"
---
# <a name="analyze-data-with-apache-spark"></a>Анализ данных с помощью Apache Spark

В этом учебнике вы узнаете, как выполнять анализ исследовательских данных с помощью Azure Open DataSet и Apache Spark, а затем визуализировать результаты в записной книжке Azure синапсе Studio.

В частности, мы проанализируем набор данных о [такси городе Нью-Йорк (Нью)](https://azure.microsoft.com/en-us/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) . Данные доступны через открытые наборы данных Azure. Это подмножество набора данных содержит сведения о желтых поездках в такси, включая сведения о каждом путешествии, начальное и конечное время и местоположения, стоимость и другие интересные атрибуты.
  
## <a name="before-you-begin"></a>Перед началом
- Создайте пул Apache Spark, следуя [учебнику создание пула Apache Spark](../articles/../quickstart-create-apache-spark-pool-studio.md) . 

## <a name="download-and-prepare-the-data"></a>Скачивание и подготовка данных
1. Создайте записную книжку, используя ядро PySpark. Инструкции см. [в разделе Создание записной книжки](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook). 
   
> [!Note]
> 
> Ядро PySpark позволяет не задавать контексты явным образом. Контекст Spark будет создан автоматически при выполнении первой ячейки кода.
>

2. В этом учебнике мы будем использовать несколько различных библиотек, чтобы помочь нам визуализировать набор данных. Чтобы выполнить этот анализ, необходимо импортировать следующие библиотеки: 

```python
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd
```

3. Поскольку необработанные данные имеют формат Parquet, можно использовать контекст Spark, чтобы извлечь файл в память напрямую в виде таблицы данных. Создайте кадр данных Spark, извлекая данные с помощью API Открытых наборов данных. Здесь мы будем использовать схему "кадры данных Spark" *для чтения* свойств, чтобы определить типы данных и схему.

```python
from azureml.opendatasets import NycTlcYellow
from datetime import datetime
from dateutil import parser

end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
df = nyc_tlc.to_spark_dataframe()
```

4. После считывания данных необходимо выполнить начальную фильтрацию для очистки набора данных. Мы можем удалить ненужные столбцы и добавить дополнительные столбцы, которые извлекают важную информацию. Кроме того, мы будем отфильтровывать аномалии в наборе данных.

```python
# Filter the dataset 
from pyspark.sql.functions import *

filtered_df = df.select('vendorID', 'passengerCount', 'tripDistance','paymentType', 'fareAmount', 'tipAmount'\
                                , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                , dayofweek('tpepPickupDateTime').alias('day_of_week')\
                                , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month'))\
                            .filter((df.passengerCount > 0)\
                                & (df.tipAmount >= 0)\
                                & (df.fareAmount >= 1) & (df.fareAmount <= 250)\
                                & (df.tripDistance > 0) & (df.tripDistance <= 200))

filtered_df.createOrReplaceTempView("taxi_dataset")
```

## <a name="analyze-data"></a>Анализ данных
 В качестве аналитика данных доступен широкий спектр средств, позволяющих извлекать ценные сведения из данных. В этой части руководства мы рассмотрим несколько полезных средств, доступных в записных книжках Azure синапсе Analytics. В этом анализе мы хотим понять факторы, которые выдают более высокие советы по использованию выбранного периода.

###  <a name="apache-spark-sql-magic"></a>Apache Spark SQL Magic 
Сначала мы выполним анализ исследовательских данных, Apache Spark команды SQL и Magic с записной книжкой синапсе. После получения запроса мы будем визуализировать результаты с помощью встроенной ```chart options``` возможности. 

1. В записной книжке создайте новую ячейку и скопируйте приведенный ниже код. С помощью этого запроса мы хотим понять, как средние суммы советов были изменены за выбранный период. Этот запрос также поможет нам определить другие полезные сведения, включая минимальное и максимальное количество советов в день и среднюю FARE сумму.
   
```sql
%%sql
SELECT 
    day_of_month
    , MIN(tipAmount) AS minTipAmount
    , MAX(tipAmount) AS maxTipAmount
    , AVG(tipAmount) AS avgTipAmount
    , AVG(fareAmount) as fareAmount
FROM taxi_dataset 
GROUP BY day_of_month
ORDER BY day_of_month ASC
```

2. После завершения выполнения запроса можно визуализировать результаты, переключившись на **представление диаграммы**. В этом примере мы создадим **график** , указав ```day_of_month``` поле в качестве **ключа** и в ```avgTipAmount``` качестве **значения**. После выбора параметров нажмите кнопку **Применить** , чтобы обновить диаграмму. 
   
## <a name="visualize-data"></a>Визуализируйте данные
В дополнение к встроенным параметрам создания диаграмм для записных книжек можно также использовать популярные библиотеки с открытым исходным кодом, чтобы создавать собственные визуализации. В следующих примерах мы будем использовать Сеаборн и Matplotlib, которые обычно используют библиотеки Python для визуализации данных. 

> [!Note]
> 
> По умолчанию каждый пул Apache Spark аналитики Azure синапсе содержит набор часто используемых и стандартных библиотек. Полный список библиотек можно просмотреть в [среде выполнения Azure синапсе](../spark/apache-spark-version-support.md). документации. Кроме того, чтобы предоставить приложениям сторонний или локально разработанный код, можно [установить библиотеку](../spark/apache-spark-azure-portal-add-libraries.md) на одном из пулов Spark.
>

1. Для упрощения и уменьшения затрат на разработку мы будем использовать образец набора данных. Мы будем использовать встроенную возможность выборки Apache Spark. Кроме того, для Сеаборн и Matplotlib требуется кадр данных Pandas или массив NumPy. Чтобы получить кадр данных Pandas, мы будем использовать команду, ```toPandas()``` чтобы преобразовать наш кадр данных.

```python
# To make development easier, faster and less expensive down sample for now
sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()
```

1. Во первых, мы хотим разобраться с распределением советов в нашем наборе данных. Мы будем использовать Matplotlib для создания гистограммы, показывающей распределение суммы и количества TIP. В зависимости от распределения мы видим, что советы отклоняются в отношении сумм, меньших или равных $10.

```python
# Look at tips by count histogram using Matplotlib

ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()
```

![Гистограмма советов](./media/apache-spark-machine-learning-mllib-notebook/histogram.png)

1. Далее мы хотим понять связь между советами по данному пути и днем недели. Мы будем использовать Сеаборн, чтобы создать блочную диаграмму с суммированием тенденций за каждый день недели. 

```python
# View the distribution of tips by day of week using Seaborn
ax = sns.boxplot(x="day_of_week", y="tipAmount",data=sampled_taxi_pd_df, showfliers = False)
ax.set_title('Tip amount distribution per day')
ax.set_xlabel('Day of Week')
ax.set_ylabel('Tip Amount ($)')
plt.show()

```
![Распространение советов в день](./media/apache-spark-data-viz/data-analyst-tutorial-per-day.png)

4. Еще одна гипотеза, на которую мы можем столкнуться, — это положительная связь между числом пассажиров и общей суммой советов по такси. Чтобы проверить эту связь, мы выполним следующий код, чтобы создать блочный график, иллюстрирующий распределение советов по каждому пассажиров счетчику. 
   
```python
# How many passengers tipped by various amounts 
ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
ax2.set_ylim(0,30)
plt.suptitle('')
plt.show()
```
![Блочная усами графика](./media/apache-spark-machine-learning-mllib-notebook/box-whisker-plot.png)

1. Наконец, нам нужно понимать отношение между суммой TIP FARE. В зависимости от результатов мы видим, что существует несколько наблюдений, которые не подсказкают людям. Однако мы также видим положительные отношения между общими суммами FARE и TIP.
   
```python
# Look at the relationship between fare and tip amounts

ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.suptitle('')
plt.show()
```
![Точечная диаграмма](./media/apache-spark-machine-learning-mllib-notebook/scatter.png)

## <a name="shut-down-the-spark-instance"></a>Завершение работы экземпляра Spark

Закончив работу с приложением, завершите работу записной книжки, чтобы освободить ресурсы. Для этого закройте вкладку или выберите **Завершить сеанс** на панели состояния в нижней части записной книжки.

## <a name="see-also"></a>См. также раздел

- [Обзор: Apache Spark в Azure Synapse Analytics](apache-spark-overview.md)
- [Создание модели машинного обучения с помощью Apache SparkML](../spark/apache-spark-machine-learning-mllib-notebook.md)

## <a name="next-steps"></a>Дальнейшие действия

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Официальная документация по Apache Spark](https://spark.apache.org/docs/latest/)