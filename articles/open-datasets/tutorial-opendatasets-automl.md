---
title: Руководство по Расширение автоматизированной модели машинного обучения
titleSuffix: Azure Open Datasets
description: Узнайте, как с помощью открытых наборов данных Azure и Службы машинного обучения Azure создать регрессионную модель для прогнозирования тарифов на такси в Нью-Йорке.
services: open-datasets
ms.service: open-datasets
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 05/02/2019
ms.openlocfilehash: 6f72daa4a601df0e3592910645c2f9b35ab64431
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845817"
---
# <a name="tutorial-build-a-regression-model-with-automated-machine-learning-and-open-datasets"></a>Руководство по Создание регрессионной модели с помощью автоматизированного машинного обучения и открытых наборов данных

В этом руководстве показано, как с помощью открытых наборов данных Azure и Службы машинного обучения Azure создать регрессионную модель для прогнозирования тарифов на такси в Нью-Йорке. Вы можете скачать общедоступные данные о поездках на такси, праздничных днях и погодных условиях, а затем настроить автоматизированный эксперимент машинного обучения с помощью Службы машинного обучения Azure. В ходе этого процесса добавляются данные для обучения и настраиваются параметры, а также выполняется автоматическая итерация с использованием комбинаций разных методов нормализации или стандартизации признаков, моделей и настроек гиперпараметров для получения оптимальной модели.

В этом руководстве описано, как выполнять такие задачи:

- Настройка рабочей области службы "Машинное обучение Azure".
- Настройка локальной среды Python.
- Получение, преобразование и объединение данных с помощью открытых наборов данных Azure.
- Обучение автоматизированной регрессионной модели машинного обучения.
- Расчет точности модели.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством необходимо следующее:

* Рабочая область службы машинного обучения Azure.
* Среда Python 3.6.

### <a name="create-a-workspace"></a>Создание рабочей области

Если у вас нет рабочей области, выполните эти [инструкции](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-workspace), чтобы создать ее с помощью портала Azure. После создания запишите имя рабочей области, имя группы ресурсов и идентификатор подписки.

### <a name="create-a-python-environment"></a>Создание среды Python

В этом примере используется среде Anaconda с записными книжками Jupyter, при этом код можно выполнять в любой среде версии 3.6.x с помощью любого текстового редактора или интегрированной среды разработки. Чтобы создать среду разработки, выполните следующие шаги.

1. Если у вас нет дистрибутива Anaconda, [скачайте](https://www.anaconda.com/distribution/) и установите его, выбрав **Python 3.7**.
1. Откройте командную строку Anaconda и создайте среду. Скачивание компонентов и пакетов и последующее создание среды займет несколько минут.
    ```
    conda create -n tutorialenv python=3.6.5
    ```
1. Активируйте среду.
    ```
    conda activate tutorialenv
    ```
1. Включите ядра IPython для своей среды.
    ```
    conda install notebook ipykernel
    ```
1. Создайте ядро.
    ```
    ipython kernel install --user
    ```
1. Установите пакеты, необходимые для этого руководства. Это пакеты большого объема, установка которых может занять 5–10 минут.
    ```
    pip install azureml-sdk[automl] azureml-opendatasets
    ```
1. Запустите ядро записной книжки в своей среде.
    ```
    jupyter notebook
    ```

После выполнения этих действий клонируйте [репозиторий с записной книжкой открытых наборов данных](https://github.com/Azure/OpenDatasetsNotebooks) и откройте записную книжку **tutorials/taxi-automl/01-tutorial-opendatasets-automl.ipynb**, чтобы его запустить.

## <a name="download-and-prepare-data"></a>Скачивание и подготовка данных

Импортируйте необходимые пакеты. Пакет открытых наборов данных содержит класс, представляющий каждый источник данных (например `NycTlcGreen`), для упрощения фильтрации параметров данных перед скачиванием.


```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Начните с создания кадра данных для хранения данных о поездках на такси. При работе в среде, отличающейся от Spark, в открытых наборах данных можно скачать за раз данные некоторых классов только за один месяц. Это ограничение позволяет избежать ошибки `MemoryError` при использовании больших наборов данных. Чтобы получить данные о поездках на такси за год, поочередно скачайте данные за каждый месяц и, прежде чем добавить данные в `green_taxi_df`, сделайте случайную выборку 2000 записей за каждый месяц, чтобы не раздувать кадр данных. После этого проверьте данные.

>[!NOTE]
> Открытые наборы данных включают классы зеркального отображения для работы в среде Spark, в которой объем данных и памяти не имеют ограничений.

```python
green_taxi_df = pd.DataFrame([])
start = datetime.strptime("1/1/2016", "%m/%d/%Y")
end = datetime.strptime("1/31/2016", "%m/%d/%Y")

for sample_month in range(12):
    temp_df_green = NycTlcGreen(start + relativedelta(months=sample_month), end + relativedelta(months=sample_month)) \
        .to_pandas_dataframe()
    green_taxi_df = green_taxi_df.append(temp_df_green.sample(2000))

green_taxi_df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>lpepPickupDatetime</th>
      <th>lpepDropoffDatetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>...</th>
      <th>paymentType</th>
      <th>fareAmount</th>
      <th>extra</th>
      <th>mtaTax</th>
      <th>improvementSurcharge</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>tripType</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20 17:38:28</td>
      <td>2016-01-20 17:46:33</td>
      <td>1</td>
      <td>0,98</td>
      <td>Нет</td>
      <td>Нет</td>
      <td>-73,921715</td>
      <td>40,766682</td>
      <td>-73,916908</td>
      <td>...</td>
      <td>2,0</td>
      <td>7.0</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>NaN</td>
      <td>8,8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1221794</th>
      <td>2</td>
      <td>2016-01-01 21:53:28</td>
      <td>2016-01-02 00:00:00</td>
      <td>1</td>
      <td>3,08</td>
      <td>Нет</td>
      <td>Нет</td>
      <td>-73,979973</td>
      <td>40,677071</td>
      <td>-73,934349</td>
      <td>...</td>
      <td>2,0</td>
      <td>11,5</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>NaN</td>
      <td>12,8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1165078</th>
      <td>2</td>
      <td>2016-01-01 00:50:23</td>
      <td>2016-01-01 01:05:37</td>
      <td>1</td>
      <td>2.44</td>
      <td>Нет</td>
      <td>Нет</td>
      <td>-73,863045</td>
      <td>40,882923</td>
      <td>-73,839836</td>
      <td>...</td>
      <td>2,0</td>
      <td>12,5</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>NaN</td>
      <td>13,8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04 17:50:03</td>
      <td>2016-01-04 18:03:43</td>
      <td>1</td>
      <td>2,87</td>
      <td>Нет</td>
      <td>Нет</td>
      <td>-73,977730</td>
      <td>40,684647</td>
      <td>-73,931259</td>
      <td>...</td>
      <td>1.0</td>
      <td>12.0</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>NaN</td>
      <td>13,8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13 08:48:20</td>
      <td>2016-01-13 08:52:16</td>
      <td>1</td>
      <td>0,50</td>
      <td>Нет</td>
      <td>Нет</td>
      <td>-73,942589</td>
      <td>40,841423</td>
      <td>-73,943672</td>
      <td>...</td>
      <td>2,0</td>
      <td>4.5.</td>
      <td>0,0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>NaN</td>
      <td>5,3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1049431</th>
      <td>2</td>
      <td>2016-01-29 17:16:18</td>
      <td>2016-01-29 17:27:52</td>
      <td>1</td>
      <td>2.25</td>
      <td>Нет</td>
      <td>Нет</td>
      <td>-73,830894</td>
      <td>40,759434</td>
      <td>-73,842422</td>
      <td>...</td>
      <td>2,0</td>
      <td>10,5</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>NaN</td>
      <td>12,3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>646563</th>
      <td>2</td>
      <td>2016-01-14 00:45:30</td>
      <td>2016-01-14 00:54:16</td>
      <td>1</td>
      <td>1,93</td>
      <td>Нет</td>
      <td>Нет</td>
      <td>-73,927109</td>
      <td>40,762848</td>
      <td>-73,909302</td>
      <td>...</td>
      <td>1.0</td>
      <td>8.5</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>NaN</td>
      <td>9,8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>438204</th>
      <td>1</td>
      <td>2016-01-09 14:25:02</td>
      <td>2016-01-09 14:32:48</td>
      <td>2</td>
      <td>0,80</td>
      <td>Нет</td>
      <td>Нет</td>
      <td>-73,881195</td>
      <td>40,741779</td>
      <td>-73,872086</td>
      <td>...</td>
      <td>2,0</td>
      <td>6,5</td>
      <td>0,0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>NaN</td>
      <td>7.3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>303784</th>
      <td>2</td>
      <td>2016-01-25 18:13:47</td>
      <td>2016-01-25 18:23:50</td>
      <td>1</td>
      <td>1,04</td>
      <td>Нет</td>
      <td>Нет</td>
      <td>-73,954376</td>
      <td>40,805729</td>
      <td>-73,939117</td>
      <td>...</td>
      <td>1.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>1.5</td>
      <td>0,0</td>
      <td>NaN</td>
      <td>11,3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>269105</th>
      <td>2</td>
      <td>2016-01-24 20:46:50</td>
      <td>2016-01-24 21:04:03</td>
      <td>6</td>
      <td>2.82</td>
      <td>Нет</td>
      <td>Нет</td>
      <td>-73,845200</td>
      <td>40,722134</td>
      <td>-73,810638</td>
      <td>...</td>
      <td>1.0</td>
      <td>13,0</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>2,0</td>
      <td>0,0</td>
      <td>NaN</td>
      <td>16,3</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
<p>10 строк × 23 столбца</p>
</div>



Теперь, когда начальные данные загружены, определите функцию для создания различных признаков на основе времени из поля с временем и датой начала поездки. При этом будут созданы новые поля для номера месяца, дня месяца, дня недели и времени дня, что позволит модели учитывать сезонные колебания на основе времени. Функция также добавляет статический признак для кода страны, позволяющий добавить данные о праздниках. Примените функцию `apply()` к кадру данных, чтобы последовательно применить функцию `build_time_features()` к каждой строке данных о поездках на такси.


```python
def build_time_features(vector):
    pickup_datetime = vector[0]
    month_num = pickup_datetime.month
    day_of_month = pickup_datetime.day
    day_of_week = pickup_datetime.weekday()
    hour_of_day = pickup_datetime.hour
    country_code = "US"

    return pd.Series((month_num, day_of_month, day_of_week, hour_of_day, country_code))


green_taxi_df[["month_num", "day_of_month", "day_of_week", "hour_of_day", "country_code"]
              ] = green_taxi_df[["lpepPickupDatetime"]].apply(build_time_features, axis=1)
green_taxi_df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>lpepPickupDatetime</th>
      <th>lpepDropoffDatetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>...</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>tripType</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>country_code</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20 17:38:28</td>
      <td>2016-01-20 17:46:33</td>
      <td>1</td>
      <td>0,98</td>
      <td>Нет</td>
      <td>Нет</td>
      <td>-73,921715</td>
      <td>40,766682</td>
      <td>-73,916908</td>
      <td>...</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>NaN</td>
      <td>8,8</td>
      <td>1.0</td>
      <td>1</td>
      <td>20</td>
      <td>2</td>
      <td>17</td>
      <td>США</td>
    </tr>
    <tr>
      <th>1221794</th>
      <td>2</td>
      <td>2016-01-01 21:53:28</td>
      <td>2016-01-02 00:00:00</td>
      <td>1</td>
      <td>3,08</td>
      <td>Нет</td>
      <td>Нет</td>
      <td>-73,979973</td>
      <td>40,677071</td>
      <td>-73,934349</td>
      <td>...</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>NaN</td>
      <td>12,8</td>
      <td>1.0</td>
      <td>1</td>
      <td>1</td>
      <td>4\.</td>
      <td>21</td>
      <td>США</td>
    </tr>
    <tr>
      <th>1165078</th>
      <td>2</td>
      <td>2016-01-01 00:50:23</td>
      <td>2016-01-01 01:05:37</td>
      <td>1</td>
      <td>2.44</td>
      <td>Нет</td>
      <td>Нет</td>
      <td>-73,863045</td>
      <td>40,882923</td>
      <td>-73,839836</td>
      <td>...</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>NaN</td>
      <td>13,8</td>
      <td>1.0</td>
      <td>1</td>
      <td>1</td>
      <td>4\.</td>
      <td>0</td>
      <td>США</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04 17:50:03</td>
      <td>2016-01-04 18:03:43</td>
      <td>1</td>
      <td>2,87</td>
      <td>Нет</td>
      <td>Нет</td>
      <td>-73,977730</td>
      <td>40,684647</td>
      <td>-73,931259</td>
      <td>...</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>NaN</td>
      <td>13,8</td>
      <td>1.0</td>
      <td>1</td>
      <td>4\.</td>
      <td>0</td>
      <td>17</td>
      <td>США</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13 08:48:20</td>
      <td>2016-01-13 08:52:16</td>
      <td>1</td>
      <td>0,50</td>
      <td>Нет</td>
      <td>Нет</td>
      <td>-73,942589</td>
      <td>40,841423</td>
      <td>-73,943672</td>
      <td>...</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>NaN</td>
      <td>5,3</td>
      <td>1.0</td>
      <td>1</td>
      <td>13</td>
      <td>2</td>
      <td>8</td>
      <td>США</td>
    </tr>
    <tr>
      <th>1049431</th>
      <td>2</td>
      <td>2016-01-29 17:16:18</td>
      <td>2016-01-29 17:27:52</td>
      <td>1</td>
      <td>2.25</td>
      <td>Нет</td>
      <td>Нет</td>
      <td>-73,830894</td>
      <td>40,759434</td>
      <td>-73,842422</td>
      <td>...</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>NaN</td>
      <td>12,3</td>
      <td>1.0</td>
      <td>1</td>
      <td>29</td>
      <td>4\.</td>
      <td>17</td>
      <td>США</td>
    </tr>
    <tr>
      <th>646563</th>
      <td>2</td>
      <td>2016-01-14 00:45:30</td>
      <td>2016-01-14 00:54:16</td>
      <td>1</td>
      <td>1,93</td>
      <td>Нет</td>
      <td>Нет</td>
      <td>-73,927109</td>
      <td>40,762848</td>
      <td>-73,909302</td>
      <td>...</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>NaN</td>
      <td>9,8</td>
      <td>1.0</td>
      <td>1</td>
      <td>14</td>
      <td>3</td>
      <td>0</td>
      <td>США</td>
    </tr>
    <tr>
      <th>438204</th>
      <td>1</td>
      <td>2016-01-09 14:25:02</td>
      <td>2016-01-09 14:32:48</td>
      <td>2</td>
      <td>0,80</td>
      <td>Нет</td>
      <td>Нет</td>
      <td>-73,881195</td>
      <td>40,741779</td>
      <td>-73,872086</td>
      <td>...</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>NaN</td>
      <td>7.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>9</td>
      <td>5</td>
      <td>14</td>
      <td>США</td>
    </tr>
    <tr>
      <th>303784</th>
      <td>2</td>
      <td>2016-01-25 18:13:47</td>
      <td>2016-01-25 18:23:50</td>
      <td>1</td>
      <td>1,04</td>
      <td>Нет</td>
      <td>Нет</td>
      <td>-73,954376</td>
      <td>40,805729</td>
      <td>-73,939117</td>
      <td>...</td>
      <td>1.5</td>
      <td>0,0</td>
      <td>NaN</td>
      <td>11,3</td>
      <td>1.0</td>
      <td>1</td>
      <td>25</td>
      <td>0</td>
      <td>18</td>
      <td>США</td>
    </tr>
    <tr>
      <th>269105</th>
      <td>2</td>
      <td>2016-01-24 20:46:50</td>
      <td>2016-01-24 21:04:03</td>
      <td>6</td>
      <td>2.82</td>
      <td>Нет</td>
      <td>Нет</td>
      <td>-73,845200</td>
      <td>40,722134</td>
      <td>-73,810638</td>
      <td>...</td>
      <td>2,0</td>
      <td>0,0</td>
      <td>NaN</td>
      <td>16,3</td>
      <td>1.0</td>
      <td>1</td>
      <td>24</td>
      <td>6</td>
      <td>20</td>
      <td>США</td>
    </tr>
  </tbody>
</table>
<p>10 строк × 28 столбцов</p>
</div>

Удалите столбцы, которые не требуются для моделирования или создания дополнительных признаков. Переименуйте поле начала поездки и уберите метку времени с помощью `pandas.Series.dt.normalize`. Это нужно сделать со всеми полями времени, чтобы компонент даты и времени можно было позднее использовать в качестве ключа для объединения наборов данных на уровне дней.

```python
columns_to_remove = ["lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                     ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df = green_taxi_df.rename(
    columns={"lpepPickupDatetime": "datetime"})
green_taxi_df["datetime"] = green_taxi_df["datetime"].dt.normalize()
green_taxi_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>Datetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>dropoffLatitude</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>country_code</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20</td>
      <td>1</td>
      <td>0,98</td>
      <td>-73,921715</td>
      <td>40,766682</td>
      <td>-73,916908</td>
      <td>40,761257</td>
      <td>8,8</td>
      <td>1</td>
      <td>20</td>
      <td>2</td>
      <td>17</td>
      <td>США</td>
    </tr>
    <tr>
      <th>1221794</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>3,08</td>
      <td>-73,979973</td>
      <td>40,677071</td>
      <td>-73,934349</td>
      <td>40,671654</td>
      <td>12,8</td>
      <td>1</td>
      <td>1</td>
      <td>4\.</td>
      <td>21</td>
      <td>США</td>
    </tr>
    <tr>
      <th>1165078</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>2.44</td>
      <td>-73,863045</td>
      <td>40,882923</td>
      <td>-73,839836</td>
      <td>40,868336</td>
      <td>13,8</td>
      <td>1</td>
      <td>1</td>
      <td>4\.</td>
      <td>0</td>
      <td>США</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04</td>
      <td>1</td>
      <td>2,87</td>
      <td>-73,977730</td>
      <td>40,684647</td>
      <td>-73,931259</td>
      <td>40,694248</td>
      <td>13,8</td>
      <td>1</td>
      <td>4\.</td>
      <td>0</td>
      <td>17</td>
      <td>США</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13</td>
      <td>1</td>
      <td>0,50</td>
      <td>-73,942589</td>
      <td>40,841423</td>
      <td>-73,943672</td>
      <td>40,834396</td>
      <td>5,3</td>
      <td>1</td>
      <td>13</td>
      <td>2</td>
      <td>8</td>
      <td>США</td>
    </tr>
  </tbody>
</table>
</div>

### <a name="enrich-with-holiday-data"></a>Добавление данных о праздниках

Теперь, когда данные о поездках на такси загружены и частично подготовлены, добавьте данные о праздничных днях в качестве дополнительных признаков. Учет данных о праздниках повысит точность модели, так как в эти дни спрос на услуги такси значительно возрастает и предложение становится ограниченным. Набор данных о праздничных днях сравнительно мал, поэтому добавьте его полностью с помощью конструктора класса `PublicHolidays`, не отфильтровывая параметры. Просмотрите данные, чтобы проверить их формат.

```python
from azureml.opendatasets import PublicHolidays
# call default constructor to download full dataset
holidays_df = PublicHolidays().to_pandas_dataframe()
holidays_df.head(5)
```

    ActivityStarted, to_pandas_dataframe
    Looking for parquet files...
    Reading them into Pandas dataframe...
    Reading Processed/part-00000-tid-1353805596865908763-9ee4e95b-0d55-4292-addd-a0e19d7c32cb-3559-c000.snappy.parquet under container holidaydatacontainer
    Done.
    ActivityCompleted: Activity=to_pandas_dataframe, HowEnded=Success, Duration=1799.89 [ms]

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>countryOrRegion</th>
      <th>holidayName</th>
      <th>isPaidTimeOff</th>
      <th>countryRegionCode</th>
      <th>normalizeHolidayName</th>
      <th>date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>40688</th>
      <td>Албания</td>
      <td>New Year's Day</td>
      <td>Нет</td>
      <td>AL</td>
      <td>New Year's Day</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40689</th>
      <td>Алжир</td>
      <td>New Year's Day</td>
      <td>Нет</td>
      <td>DZ</td>
      <td>New Year's Day</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40690</th>
      <td>Андорра</td>
      <td>New Year's Day</td>
      <td>Нет</td>
      <td>AD</td>
      <td>New Year's Day</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40691</th>
      <td>Ангола</td>
      <td>New Year's Day</td>
      <td>Нет</td>
      <td>АО</td>
      <td>New Year's Day</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40692</th>
      <td>Аргентина</td>
      <td>New Year's Day</td>
      <td>Нет</td>
      <td>AR</td>
      <td>New Year's Day</td>
      <td>2008-01-01</td>
    </tr>
  </tbody>
</table>
</div>



Переименуйте столбцы `countryRegionCode` и `date`, чтобы они совпадали с именами соответствующих полей в данных о поездках на такси, а также нормализуйте данные о времени, чтобы их можно было использовать в качестве ключа. Затем объедините данные о праздниках с данными о поездках на такси, выполнив операцию левого соединения с помощью функции Pandas `merge()`. В результате все записи из `green_taxi_df` будут сохранены, и к ним добавятся имеющиеся данные о праздничных днях для соответствующих параметров `datetime` и `country_code`. Последний в нашем случае имеет только значение `"US"`. Просмотрите данных, чтобы проверить, правильно ли выполнено объединение.

```python
holidays_df = holidays_df.rename(
    columns={"countryRegionCode": "country_code", "date": "datetime"})
holidays_df["datetime"] = holidays_df["datetime"].dt.normalize()
holidays_df.pop("countryOrRegion")
holidays_df.pop("holidayName")

taxi_holidays_df = pd.merge(green_taxi_df, holidays_df, how="left", on=[
                            "datetime", "country_code"])
taxi_holidays_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>Datetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>dropoffLatitude</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>country_code</th>
      <th>isPaidTimeOff</th>
      <th>normalizeHolidayName</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2016-01-20</td>
      <td>1</td>
      <td>0,98</td>
      <td>-73,921715</td>
      <td>40,766682</td>
      <td>-73,916908</td>
      <td>40,761257</td>
      <td>8,8</td>
      <td>1</td>
      <td>20</td>
      <td>2</td>
      <td>17</td>
      <td>США</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>3,08</td>
      <td>-73,979973</td>
      <td>40,677071</td>
      <td>-73,934349</td>
      <td>40,671654</td>
      <td>12,8</td>
      <td>1</td>
      <td>1</td>
      <td>4\.</td>
      <td>21</td>
      <td>США</td>
      <td>Истина</td>
      <td>New Year's Day</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>2.44</td>
      <td>-73,863045</td>
      <td>40,882923</td>
      <td>-73,839836</td>
      <td>40,868336</td>
      <td>13,8</td>
      <td>1</td>
      <td>1</td>
      <td>4\.</td>
      <td>0</td>
      <td>США</td>
      <td>Истина</td>
      <td>New Year's Day</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2016-01-04</td>
      <td>1</td>
      <td>2,87</td>
      <td>-73,977730</td>
      <td>40,684647</td>
      <td>-73,931259</td>
      <td>40,694248</td>
      <td>13,8</td>
      <td>1</td>
      <td>4\.</td>
      <td>0</td>
      <td>17</td>
      <td>США</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4\.</th>
      <td>1</td>
      <td>2016-01-13</td>
      <td>1</td>
      <td>0,50</td>
      <td>-73,942589</td>
      <td>40,841423</td>
      <td>-73,943672</td>
      <td>40,834396</td>
      <td>5,3</td>
      <td>1</td>
      <td>13</td>
      <td>2</td>
      <td>8</td>
      <td>США</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>

### <a name="enrich-with-weather-data"></a>Добавление данных о погоде

Теперь к данным о поездках на такси и данным о праздниках мы добавим данные о погоде, собираемые Национальным управлением океанических и атмосферных исследований. Добавьте данные аналогичным образом, последовательно скачивая данные за каждый месяц. Кроме того, укажите параметр `cols` с массивом строк, чтобы отфильтровать столбцы, которые вы хотите скачать. Это очень большой набор данных, содержащий данных о погоде во всем мире, поэтому перед добавлением данных за каждый месяц, отфильтруйте их по полям географической широты и долготы Нью-Йорка с помощью функции `query()` в кадре данных. Так вы сократите размер `weather_df`.

```python
from azureml.opendatasets import NoaaIsdWeather

weather_df = pd.DataFrame([])
start = datetime.strptime("1/1/2016", "%m/%d/%Y")
end = datetime.strptime("1/31/2016", "%m/%d/%Y")

for sample_month in range(12):
    tmp_df = NoaaIsdWeather(cols=["temperature", "precipTime", "precipDepth", "snowDepth"], start_date=start + relativedelta(months=sample_month), end_date=end + relativedelta(months=sample_month))\
        .to_pandas_dataframe()
    print("--weather downloaded--")

    # filter out coordinates not in NYC to conserve memory
    tmp_df = tmp_df.query("latitude>=40.53 and latitude<=40.88")
    tmp_df = tmp_df.query("longitude>=-74.09 and longitude<=-73.72")
    print("--filtered coordinates--")
    weather_df = weather_df.append(tmp_df)

weather_df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>wban</th>
      <th>precipTime</th>
      <th>snowDepth</th>
      <th>Температура</th>
      <th>latitude</th>
      <th>precipDepth</th>
      <th>longitude</th>
      <th>Datetime</th>
      <th>usaf</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1754979</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>7,2</td>
      <td>40,85</td>
      <td>0,0</td>
      <td>-74,061</td>
      <td>2016-01-01 00:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754980</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.7</td>
      <td>40,85</td>
      <td>0,0</td>
      <td>-74,061</td>
      <td>2016-01-01 01:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754981</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.7</td>
      <td>40,85</td>
      <td>0,0</td>
      <td>-74,061</td>
      <td>2016-01-01 02:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754982</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.1</td>
      <td>40,85</td>
      <td>0,0</td>
      <td>-74,061</td>
      <td>2016-01-01 03:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754983</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.6</td>
      <td>40,85</td>
      <td>0,0</td>
      <td>-74,061</td>
      <td>2016-01-01 04:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754984</th>
      <td>94741</td>
      <td>24,0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40,85</td>
      <td>5.0</td>
      <td>-74,061</td>
      <td>2016-01-01 04:59:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754985</th>
      <td>94741</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40,85</td>
      <td>NaN</td>
      <td>-74,061</td>
      <td>2016-01-01 04:59:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754986</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.6</td>
      <td>40,85</td>
      <td>0,0</td>
      <td>-74,061</td>
      <td>2016-01-01 05:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754987</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.0</td>
      <td>40,85</td>
      <td>0,0</td>
      <td>-74,061</td>
      <td>2016-01-01 06:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754988</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.0</td>
      <td>40,85</td>
      <td>0,0</td>
      <td>-74,061</td>
      <td>2016-01-01 07:51:00</td>
      <td>725025</td>
    </tr>
  </tbody>
</table>
</div>

Снова вызовите `pandas.Series.dt.normalize` для поля `datetime` в данных о погоде, чтобы обеспечить соответствие с ключом времени в `taxi_holidays_df`. Удалите ненужные столбцы и отфильтруйте записи, в которых значение температура не является числом (`NaN`).

Затем сгруппируйте данные о погоде, чтобы получить агрегированные данные по дням. Определите словарь `aggregations` для определения способов агрегирования данных в каждом поле по дням. Для `snowDepth` и `temperature` примите среднее значение, а для `precipTime` и `precipDepth` — максимальное значение за день. Используйте функцию `groupby()` наряду с агрегированием, чтобы сгруппировать данные. Проверьте данные и убедитесь в том, что для каждого дня имеется только одна запись.

```python
weather_df["datetime"] = weather_df["datetime"].dt.normalize()
weather_df.pop("usaf")
weather_df.pop("wban")
weather_df.pop("longitude")
weather_df.pop("latitude")

# filter out NaN
weather_df = weather_df.query("temperature==temperature")

# group by datetime
aggregations = {"snowDepth": "mean", "precipTime": "max",
                "temperature": "mean", "precipDepth": "max"}
weather_df_grouped = weather_df.groupby("datetime").agg(aggregations)
weather_df_grouped.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>snowDepth</th>
      <th>precipTime</th>
      <th>Температура</th>
      <th>precipDepth</th>
    </tr>
    <tr>
      <th>Datetime</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2016-01-01</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>5,197345</td>
      <td>0,0</td>
    </tr>
    <tr>
      <th>2016-01-02</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>2,567857</td>
      <td>0,0</td>
    </tr>
    <tr>
      <th>2016-01-03</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>3,846429</td>
      <td>0,0</td>
    </tr>
    <tr>
      <th>2016-01-04</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>0,123894</td>
      <td>0,0</td>
    </tr>
    <tr>
      <th>2016-01-05</th>
      <td>NaN</td>
      <td>6,0</td>
      <td>-7,206250</td>
      <td>0,0</td>
    </tr>
    <tr>
      <th>2016-01-06</th>
      <td>NaN</td>
      <td>6,0</td>
      <td>-0,896396</td>
      <td>0,0</td>
    </tr>
    <tr>
      <th>2016-01-07</th>
      <td>NaN</td>
      <td>6,0</td>
      <td>3,180645</td>
      <td>0,0</td>
    </tr>
    <tr>
      <th>2016-01-08</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>4,384091</td>
      <td>0,0</td>
    </tr>
    <tr>
      <th>2016-01-09</th>
      <td>NaN</td>
      <td>6,0</td>
      <td>6,710274</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>2016-01-10</th>
      <td>NaN</td>
      <td>24,0</td>
      <td>10,943655</td>
      <td>254,0</td>
    </tr>
  </tbody>
</table>
</div>

> [!NOTE]
> В примерах этого руководства слияние данных выполняется с помощью функций Pandas и частных методов агрегирования данных, тогда как пакет SDK открытых наборов данных содержит классы, позволяющие легко выполнять слияние и расширение наборов данных. Примеры кода этих конструктивных шаблонов см. в этой [записной книжке](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb).

### <a name="cleanse-data"></a>Удаление данных

Объедините подготовленные данные о поездках на такси и праздниках с данными о погоде. В этот раз вам понадобится только ключ `datetime`. Также нужно будет повторить операцию левого соединения данных. Примените функцию `describe()` к новому кадру данных, чтобы просмотреть сводные статистические данные для каждого поля.

```python
taxi_holidays_weather_df = pd.merge(
    taxi_holidays_df, weather_df_grouped, how="left", on=["datetime"])
taxi_holidays_weather_df.describe()
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>dropoffLatitude</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>snowDepth</th>
      <th>precipTime</th>
      <th>Температура</th>
      <th>precipDepth</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>1671,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>1,786583</td>
      <td>6,576208</td>
      <td>1,582588</td>
      <td>20,505491</td>
      <td>84,936413</td>
      <td>-36,232825</td>
      <td>21,723144</td>
      <td>7,863018</td>
      <td>6,500000</td>
      <td>15,113708</td>
      <td>3,240250</td>
      <td>13,664125</td>
      <td>11,764141</td>
      <td>13,258875</td>
      <td>13,903524</td>
      <td>1056,644458</td>
    </tr>
    <tr>
      <th>std</th>
      <td>0,409728</td>
      <td>9,086857</td>
      <td>2,418177</td>
      <td>108,847821</td>
      <td>70,678506</td>
      <td>37,650276</td>
      <td>19,104384</td>
      <td>10,648766</td>
      <td>3,452124</td>
      <td>8,485155</td>
      <td>1,956895</td>
      <td>6,650676</td>
      <td>15,651884</td>
      <td>10,339720</td>
      <td>9,474396</td>
      <td>2815,592754</td>
    </tr>
    <tr>
      <th>Min</th>
      <td>1,000000</td>
      <td>-60,000000</td>
      <td>-1,000000</td>
      <td>-74,179482</td>
      <td>0,000000</td>
      <td>-74,190704</td>
      <td>0,000000</td>
      <td>-52,800000</td>
      <td>1,000000</td>
      <td>1,000000</td>
      <td>0,000000</td>
      <td>0,000000</td>
      <td>3,000000</td>
      <td>1,000000</td>
      <td>-13,379464</td>
      <td>0,000000</td>
    </tr>
    <tr>
      <th>25 %</th>
      <td>2,000000</td>
      <td>1,000000</td>
      <td>0,330000</td>
      <td>-73,946680</td>
      <td>40,717712</td>
      <td>-73,945429</td>
      <td>1,770000</td>
      <td>1,000000</td>
      <td>3,750000</td>
      <td>8,000000</td>
      <td>2,000000</td>
      <td>9,000000</td>
      <td>3,000000</td>
      <td>1,000000</td>
      <td>6,620773</td>
      <td>0,000000</td>
    </tr>
    <tr>
      <th>50 %</th>
      <td>2,000000</td>
      <td>4,000000</td>
      <td>0,830000</td>
      <td>1,500000</td>
      <td>40,814129</td>
      <td>0,500000</td>
      <td>21,495000</td>
      <td>2,000000</td>
      <td>6,500000</td>
      <td>15,000000</td>
      <td>3,000000</td>
      <td>15,000000</td>
      <td>4,428571</td>
      <td>6,000000</td>
      <td>13,090753</td>
      <td>10,000000</td>
    </tr>
    <tr>
      <th>75 %</th>
      <td>2,000000</td>
      <td>9,000000</td>
      <td>1,870000</td>
      <td>89,000000</td>
      <td>129,000000</td>
      <td>1,000000</td>
      <td>40,746146</td>
      <td>11,300000</td>
      <td>9,250000</td>
      <td>22,000000</td>
      <td>5,000000</td>
      <td>19,000000</td>
      <td>12,722222</td>
      <td>24,000000</td>
      <td>22,944737</td>
      <td>132,000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2,000000</td>
      <td>460,000000</td>
      <td>51,950000</td>
      <td>265,000000</td>
      <td>265,000000</td>
      <td>6,000000</td>
      <td>58,600000</td>
      <td>498,000000</td>
      <td>12,000000</td>
      <td>30,000000</td>
      <td>6,000000</td>
      <td>23,000000</td>
      <td>67,090909</td>
      <td>24,000000</td>
      <td>31,303665</td>
      <td>9999,000000</td>
    </tr>
  </tbody>
</table>
</div>

Из сводных статистических данных видно, что в некоторых полях присутствуют выбросы или значения, снижающие точность модели. Во-первых, выполните фильтрацию полей долготы и широты с помощью значений, которые вы использовали в данных о погоде. В поле `tripDistance` присутствуют несколько недопустимых значений, так как минимальное значение является отрицательным. В поле `passengerCount` также содержатся некорректные данные, так как максимальное количество пассажиров равно 210. Наконец, в поле `totalAmount` есть отрицательные значения, что выглядит абсурдно в контексте нашей модели.

Отфильтруйте эти аномалии с помощью функций запросов, а затем удалите несколько последних столбцов, не требуемых для обучения.

```python
final_df = taxi_holidays_weather_df.query(
    "pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query(
    "pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>0 and tripDistance<75")
final_df = final_df.query("passengerCount>0 and passengerCount<100")
final_df = final_df.query("totalAmount>0")

columns_to_remove_for_training = ["datetime", "pickupLongitude",
                                  "pickupLatitude", "dropoffLongitude", "dropoffLatitude", "country_code"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

Снова вызовите `describe()`, чтобы убедиться в том, что данные очищены должным образом. Теперь у вас есть подготовленный набор очищенных данных о поездках на такси, праздничных днях и погоде для обучения модели машинного обучения.

```python
final_df.describe()
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>snowDepth</th>
      <th>precipTime</th>
      <th>Температура</th>
      <th>precipDepth</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>1490,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>1,786910</td>
      <td>1,343476</td>
      <td>2,848488</td>
      <td>14,689039</td>
      <td>3,499788</td>
      <td>14,948916</td>
      <td>3,234254</td>
      <td>13,647344</td>
      <td>12,508581</td>
      <td>11,855929</td>
      <td>10,301433</td>
      <td>208,432384</td>
    </tr>
    <tr>
      <th>std</th>
      <td>0,409508</td>
      <td>1,001232</td>
      <td>2,895960</td>
      <td>10,289832</td>
      <td>1,707865</td>
      <td>8,442438</td>
      <td>1,958477</td>
      <td>6,640280</td>
      <td>16,203195</td>
      <td>10,125701</td>
      <td>8,553512</td>
      <td>1284,892832</td>
    </tr>
    <tr>
      <th>Min</th>
      <td>1,000000</td>
      <td>1,000000</td>
      <td>0,010000</td>
      <td>3,300000</td>
      <td>1,000000</td>
      <td>1,000000</td>
      <td>0,000000</td>
      <td>0,000000</td>
      <td>3,000000</td>
      <td>1,000000</td>
      <td>-13,379464</td>
      <td>0,000000</td>
    </tr>
    <tr>
      <th>25 %</th>
      <td>2,000000</td>
      <td>1,000000</td>
      <td>1,070000</td>
      <td>8,160000</td>
      <td>2,000000</td>
      <td>8,000000</td>
      <td>2,000000</td>
      <td>9,000000</td>
      <td>3,000000</td>
      <td>1,000000</td>
      <td>3,504580</td>
      <td>0,000000</td>
    </tr>
    <tr>
      <th>50 %</th>
      <td>2,000000</td>
      <td>1,000000</td>
      <td>1,900000</td>
      <td>11,300000</td>
      <td>3,000000</td>
      <td>15,000000</td>
      <td>3,000000</td>
      <td>15,000000</td>
      <td>4,250000</td>
      <td>6,000000</td>
      <td>10,168182</td>
      <td>3,000000</td>
    </tr>
    <tr>
      <th>75 %</th>
      <td>2,000000</td>
      <td>1,000000</td>
      <td>3,550000</td>
      <td>17,800000</td>
      <td>5,000000</td>
      <td>22,000000</td>
      <td>5,000000</td>
      <td>19,000000</td>
      <td>15,647059</td>
      <td>24,000000</td>
      <td>16,966923</td>
      <td>41,000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2,000000</td>
      <td>6,000000</td>
      <td>51,950000</td>
      <td>150,300000</td>
      <td>6,000000</td>
      <td>30,000000</td>
      <td>6,000000</td>
      <td>23,000000</td>
      <td>67,090909</td>
      <td>24,000000</td>
      <td>26,524107</td>
      <td>9999,000000</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="train-a-model"></a>Обучение модели

Теперь вы обучите автоматизированную модель машинного обучения с помощью подготовленных данных. Начните с разделения `final_df` на признаки (значения X) и метки (значения Y), которыми для этой модели является тариф на проезд в такси.

```python
y_df = final_df.pop("totalAmount")
x_df = final_df
```

Теперь разделите данные на обучающий и тестовый наборы с помощью функции `train_test_split()` из библиотеки `scikit-learn`. Параметр `test_size` определяет процент данных, выделяемых для тестирования. Параметр `random_state` задает начальное значение для случайного генератора чисел, чтобы разделение тестового обучения было детерминированным.


```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(
    x_df, y_df, test_size=0.2, random_state=222)
```

### <a name="load-workspace-and-configure-experiment"></a>Загрузка рабочей области и настройка эксперимента

Загрузите рабочую область Службы машинного обучения Azure с помощью функции `get()`, используя сведения о своей подписке и рабочей области. Создайте эксперимент в рабочей области для хранения и мониторинга выполнения модели.


```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment

workspace = Workspace.get(subscription_id="<your-subscription-id>",
                          name="<your-workspace-name>", resource_group="<your-resource-group>")
experiment = Experiment(workspace, "opendatasets-ml")
```

Создайте объект конфигурации для эксперимента с помощью класса `AutoMLConfig`. Присоедините обучающие данные и дополнительно укажите настройки и параметры, управляющие процессом обучения. Эти параметры имеют следующее предназначение:

* `task` — тип выполняемого эксперимента.
* `X` — обучающие признаки.
* `y` — обучающие метки.
* `iterations` — число выполняемых итераций. При каждой итерации испытывается комбинация различных методов нормализации или стандартизации признаков и разные модели с использованием нескольких настроек гиперпараметров.
* `primary_metric` — основная метрика для оптимизации во время обучения модели. На основе этой метрики определяется оптимальная модель.
* `preprocess` — отвечает за то, может ли в ходе эксперимента выполнятся предварительная обработка входных данных (обработка отсутствующих данных, преобразование текста в числовой тип и т. д.).
* `n_cross_validations`: Количество разделений перекрестных проверок, которые нужно выполнить, если данные проверки не указаны.


```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task="regression",
                             X=X_train.values,
                             y=y_train.values.flatten(),
                             iterations=20,
                             primary_metric="spearman_correlation",
                             preprocess=True,
                             n_cross_validations=5
                             )
```

### <a name="submit-experiment"></a>Отправка эксперимента

Отправьте эксперимент для обучения. После отправки эксперимента процесс повторяется в различных алгоритмах машинного обучения и значениях гиперпараметров в соответствии с заданными вами ограничениями. Путем оптимизации установленной метрики точности определяется оптимальная модель. Передайте в эксперимент объект `automl_config`. Для параметра отображения выходных данных задайте значение `True`, чтобы отслеживать ход выполнения эксперимента.

После отправки эксперимента можно отслеживать процесс обучения. Для каждой итерации отображается тип модели, метод нормализации или стандартизации признаков, продолжительность выполнения и точность обучения. Поле `BEST` отслеживает лучшую оценку выполнения обучения на основе вашего типа метрики.

```python
training_run = experiment.submit(automl_config, show_output=True)
```

    Running on local machine
    Parent Run ID: AutoML_5c35f2a7-e479-4e7f-a131-ed4cb51e29d1
    Current status: DatasetEvaluation. Gathering dataset statistics.
    Current status: FeaturesGeneration. Generating features for the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetCrossValidationSplit. Generating individually featurized CV splits.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: ModelSelection. Beginning model selection.

    ****************************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ****************************************************************************************************

     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   MaxAbsScaler RandomForest                      0:00:07       0.9081    0.9081
             1   StandardScalerWrapper DecisionTree             0:00:05       0.9121    0.9121
             2   StandardScalerWrapper LightGBM                 0:00:04       0.9318    0.9318
             3   StandardScalerWrapper LightGBM                 0:00:07       0.9286    0.9318
             4   MaxAbsScaler LightGBM                          0:00:05       0.9246    0.9318
             5   MaxAbsScaler LightGBM                          0:00:05       0.9199    0.9318
             6   MaxAbsScaler RandomForest                      0:00:07       0.9327    0.9327
             7   StandardScalerWrapper ElasticNet               0:00:04       0.9371    0.9371
             8   MaxAbsScaler LightGBM                          0:00:05       0.9327    0.9371
             9   MaxAbsScaler SGD                               0:00:04       0.9077    0.9371
            10   MaxAbsScaler LightGBM                          0:00:04       0.9340    0.9371
            11   StandardScalerWrapper LightGBM                 0:00:04       0.8301    0.9371
            12   MaxAbsScaler DecisionTree                      0:00:05       0.9214    0.9371
            13   StandardScalerWrapper DecisionTree             0:00:04       0.9201    0.9371
            14   MaxAbsScaler DecisionTree                      0:00:05       0.9179    0.9371
            15   MaxAbsScaler ExtremeRandomTrees                0:00:05       0.9052    0.9371
            16   StandardScalerWrapper DecisionTree             0:00:04       0.9282    0.9371
            17   StandardScalerWrapper ElasticNet               0:00:04       0.9319    0.9371
            18   VotingEnsemble                                 0:00:16       0.9380    0.9380
            19   StackEnsemble                                  0:00:17       0.9376    0.9380

### <a name="retrieve-the-fitted-model"></a>Получение подходящей модели

В конце всех итераций обучения автоматизированный процесс машинного обучения создает ансамблевый алгоритм по результатам каждой итерации с помощью бэггинга или стекинга. Извлеките подходящий ансамбль в переменную `fitted_model`, а результаты наилучшей итерации в переменную `best_run`.

```python
best_run, fitted_model = training_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="test-model-accuracy"></a>Проверка точности модели

С помощью подходящей ансамблевой модели выполните прогнозирование тарифов на такси в тестовом наборе данных. Функция `predict()` использует подходящую модель и прогнозирует значения Y (тариф на проезд) для набора данных `X_test`.


```python
y_predict = fitted_model.predict(X_test.values)
```

Рассчитайте среднеквадратическую погрешность результатов. Преобразуйте эти значения в список `y_actual` с помощью кадра данных `y_test`, чтобы сравнить их с прогнозируемыми. Функция `mean_squared_error` принимает два массива значений и вычисляет значение среднеквадратической погрешности между ними. Извлечение квадратного корня из результата дает погрешность в единицах переменной Y (тариф). Она позволяет примерно оценить, насколько спрогнозированные тарифы на такси отличаются от фактических.


```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```




    4.178568987067901



Чтобы вычислить MAPE (средняя абсолютная погрешность в процентах), запустите следующий код для полных наборов данных `y_actual` и `y_predict`. Эта метрика вычисляет абсолютное отклонение между каждой парой прогнозируемого и фактического значения, а затем суммирует все отклонения. Затем эта сумма выражается в процентах от общей суммы фактических значений.


```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

    Model MAPE:
    0.14923619644924357

    Model Accuracy:
    0.8507638035507564

Так как использовалась сравнительно небольшая выборка данных из полного набора данных (n=11748), точность модели оказалась довольно высокой — 85 % со среднеквадратической погрешностью в ±4,00 долл. США при прогнозировании тарифов на такси. Чтобы повысить точность модели, вы можете вернуться к шагу скачивания и подготовки данных, увеличить размер выборки с первоначальных 2000 записей за месяц и повторить эксперимент, чтобы переобучить модель на большем объеме данных.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не планируете использовать созданные ресурсы, удалите их, чтобы с вас не взималась плата.

1. На портале Azure выберите **Группы ресурсов** в левой части окна.
1. В списке выберите созданную группу ресурсов.
1. Выберите **Удалить группу ресурсов**.
1. Введите имя группы ресурсов. Теперь щелкните **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные примеры кода см в [записных книжках](https://github.com/Azure/OpenDatasetsNotebooks) открытых наборов данных Azure.
* Выполните это [практическое руководство](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train), чтобы получить больше сведений об автоматизированном машинном обучении в Службе машинного обучения Azure.
