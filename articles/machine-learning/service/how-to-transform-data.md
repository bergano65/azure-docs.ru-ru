---
title: 'Преобразование: пакет SDK Python для подготовки данных'
titleSuffix: Azure Machine Learning service
description: Дополнительные сведения о преобразовании и очистке данных с помощью пакета SDK для подготовки данных машинного обучения Azure. Чтобы добавлять столбцы, отфильтровывать нежелательные строки или столбцы и ввести отсутствующие значения, используйте методы преобразования.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 07/16/2019
ms.custom: seodec18
ms.openlocfilehash: 339a4f93d45b3d3b3e242aa735ce4b737a9292f0
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035958"
---
# <a name="transform-data-with-the-azure-machine-learning-data-prep-sdk"></a>Преобразование данных с помощью пакета SDK для подготовки данных машинного обучения Azure

В этой статье вы узнаете о различных методах преобразования данных с помощью `azureml-dataprep` пакета. Пакет содержит функции, упрощающие Добавление столбцов, фильтрацию ненужных строк или столбцов, а также аппроксимация отсутствующие значения. См. полную справочную документацию по [пакету azureml-для подготовки](https://aka.ms/data-prep-sdk)к просмотру.

> [!Important]
> Если вы создаете новое решение, попытайтесь преобразовать данные, моментальные снимки и сохранить версии определений наборов данных с помощью [машинное обучение Azure наборов](how-to-explore-prepare-data.md) данных (Предварительная версия). Наборы данных — это следующая версия пакета SDK для подготовки к данным, предлагающая расширенные функциональные возможности для управления DataSet-наборами в решениях искусственного интеллекта.
> Если вы используете `azureml-dataprep` пакет для создания потока данных с преобразованиями вместо того, чтобы использовать этот `azureml-datasets` пакет для создания DataSet, вы не сможете использовать моментальные снимки или версии данных с более поздними версиями.

В этом пошаговом окне приведены примеры для следующих задач.

- Добавление столбца с помощью выражения
- [Добавление отсутствующих значений](#impute-missing-values)
- [Получение столбца по образцу](#derive-column-by-example)
- [Фильтрация](#filtering)
- [Пользовательские преобразования Python](#custom-python-transforms)

## <a name="add-column-using-an-expression"></a>Добавление столбца с помощью выражения

Пакет SDK для подготовки данных машинного обучения Azure включает выражения `substring`, которые можно использовать для вычисления значения из существующих столбцов и потом указать это значение в новом столбце. В этом примере мы загрузим данные и попытаемся добавить столбцы к входным данным.

```Python
import azureml.dataprep as dprep

# loading data
dflow = dprep.read_csv(path=r'data\crime0-10.csv')
dflow.head(3)
```

||id|Серийный номер|Дата|Заблокировать|IUCR|Основной тип|Описание|Описание расположения|Фикс.|Дом|...|Административный район|Жилой микрорайон|Код ФБР|Координата X|Координата Y|Год|Обновлено|Широта|Долгота|Местоположение|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
|0|10140490|HY329907|05.07.2015 23:50:00|050XX N NEWLAND AVE|0820|КРАЖА|500 ДОЛЛ. США И МЕНЬШЕ|УЛИЦА|false|false|...|41|10|06|1129230|1933315|2015|12.07.2015 12:42:46|41.973309466|-87.800174996|(41.973309466, -87.800174996)|
|1|10139776|HY329265|05.07.2015 23:30:00|011XX W MORSE AVE|0460|АККУМУЛЯТОР|ПРОСТОЙ|УЛИЦА|false|true|...|49|1|08B|1167370|1946271|2015|12.07.2015 12:42:46|42.008124017|-87.65955018|(42.008124017, -87.65955018)|
|2|10140270|HY329253|05.07.2015 23:20:00|121XX S FRONT AVE|0486|АККУМУЛЯТОР|ПРОСТОЙ АККУМУЛЯТОР ДЛЯ ДОМАШНЕГО ПОЛЬЗОВАНИЯ|УЛИЦА|false|true|...|9|53|08B|||2015|12.07.2015 12:42:46|


Используйте выражение `substring(start, length)` для извлечения префикса из столбца "Номер регистра" и помещения этой строки в новый столбец `Case Category`. Передача переменной `substring_expression` параметру `expression` создает новый вычисляемый столбец, который выполняет выражение в каждой записи.

```Python
substring_expression = dprep.col('Case Number').substring(0, 2)
case_category = dflow.add_column(new_column_name='Case Category',
                                    prior_column='Case Number',
                                    expression=substring_expression)
case_category.head(3)
```

||id|Серийный номер|Категория регистра|Дата|Заблокировать|IUCR|Основной тип|Описание|Описание расположения|Фикс.|Дом|...|Административный район|Жилой микрорайон|Код ФБР|Координата X|Координата Y|Год|Обновлено|Широта|Долгота|Местоположение|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|HY|05.07.2015 23:50:00|050XX N NEWLAND AVE|0820|КРАЖА|500 ДОЛЛ. США И МЕНЬШЕ|УЛИЦА|false|false|...|41|10|06|1129230|1933315|2015|12.07.2015 12:42:46|41.973309466|-87.800174996|(41.973309466, -87.800174996)|
|1|10139776|HY329265|HY|05.07.2015 23:30:00|011XX W MORSE AVE|0460|АККУМУЛЯТОР|ПРОСТОЙ|УЛИЦА|false|true|...|49|1|08B|1167370|1946271|2015|12.07.2015 12:42:46|42.008124017|-87.65955018|(42.008124017, -87.65955018)|
|2|10140270|HY329253|HY|05.07.2015 23:20:00|121XX S FRONT AVE|0486|АККУМУЛЯТОР|ПРОСТОЙ АККУМУЛЯТОР ДЛЯ ДОМАШНЕГО ПОЛЬЗОВАНИЯ|УЛИЦА|false|true|...|9|53|08B|||2015|12.07.2015 12:42:46|


Используйте выражение `substring(start)` для извлечения только числа из столбца "Номер регистра" и создания нового столбца. Преобразуйте его в тип числовых данных с помощью функции `to_number()` и передайте имя строки как параметр.

```Python
substring_expression2 = dprep.col('Case Number').substring(2)
case_id = dflow.add_column(new_column_name='Case Id',
                              prior_column='Case Number',
                              expression=substring_expression2)
case_id = case_id.to_number('Case Id')
```

## <a name="impute-missing-values"></a>Добавление отсутствующих значений

Пакет SDK может замещать отсутствующие значения в указанных столбцах. В этом примере вы загрузите значения широты и долготы, а затем попытаетесь ввести отсутствующие значения во входные данные.

```python
import azureml.dataprep as dprep

# loading input data
dflow = dprep.read_csv(r'data\crime0-10.csv')
dflow = dflow.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
dflow = dflow.to_number(['Latitude', 'Longitude'])
dflow.head(3)
```

||id|Фикс.|Широта|Долгота|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|NaN|NaN|

В третьей записи отсутствуют значения широты и долготы. Чтобы аппроксимация отсутствующие значения, используйте [`ImputeMissingValuesBuilder`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) для изучения фиксированного выражения. Можно добавить в столбцы следующие значения: рассчитанное `MIN`, `MAX`, `MEAN` или `CUSTOM`. Если указано `group_by_columns`, то отсутствующие значения будут добавляться группой вместе с вычисленными `MIN`, `MAX` и `MEAN` для каждой группы.

Проверьте значение столбца широты [`summarize()`](/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-ml-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow--summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) с помощью функции. `MEAN` Эта функция принимает массив столбцов в параметре `group_by_columns`, чтобы указать совокупный уровень. Параметр `summary_columns` принимает вызов `SummaryColumnsValue`. Вызов этой функции указывает текущее имя столбца, имя нового вычисляемого поля и `SummaryFunction` для выполнения.

```python
dflow_mean = dflow.summarize(group_by_columns=['Arrest'],
                             summary_columns=[dprep.SummaryColumnsValue(column_id='Latitude',
                                                                        summary_column_name='Latitude_MEAN',
                                                                        summary_function=dprep.SummaryFunction.MEAN)])
dflow_mean = dflow_mean.filter(dprep.col('Arrest') == 'false')
dflow_mean.head(1)
```

||Фикс.|Latitude_MEAN|
|-----|-----|----|
|0|false|41.878961|

Значение `MEAN` широты выглядит точным. Добавьте его с помощью функции `ImputeColumnArguments`. Эта функция принимает строку `column_id` и `ReplaceValueFunction` для указания типа добавления. Если значение широты отсутствует, добавьте число 42, используя внешний источник данных.

Шаги добавления можно связывать друг с другом в объект `ImputeMissingValuesBuilder`, используя функцию построителя `impute_missing_values()`. Параметр `impute_columns` принимает массив объектов `ImputeColumnArguments`. Вызовите функцию `learn()` для хранения шагов добавления, а затем примените к объекту потока данных с помощью `to_dataflow()`.

```python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id='Latitude',
                                          impute_function=dprep.ReplaceValueFunction.MEAN)
# impute with custom value 42
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=42)
# get instance of ImputeMissingValuesBuilder
impute_builder = dflow.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                      group_by_columns=['Arrest'])

impute_builder.learn()
dflow_imputed = impute_builder.to_dataflow()
dflow_imputed.head(3)
```

||id|Фикс.|Широта|Долгота|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|41.878961|42.000000|

Как показано в приведенном выше результате, отсутствующая широта была добавлена с помощью значения `MEAN` группы `Arrest=='false'`. Для отсутствующего значения долготы было использовано число 42.

```python
imputed_longitude = dflow_imputed.to_pandas_dataframe()['Longitude'][2]
assert imputed_longitude == 42
```

## <a name="derive-column-by-example"></a>Получение столбцов по образцу

Одним из наиболее передовых инструментов пакета SDK для подготовки данных машинного обучения Azure является способность выводить столбцы, используя примеры желаемых результатов. Это позволяет дать пакету SDK пример, используя который он сможет сгенерировать код для достижения нужного преобразования.

```python
import azureml.dataprep as dprep
dflow = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/BostonWeather.csv')
dflow.head(4)
```

||DATE|REPORTTPYE;|HOURLYDRYBULBTEMPF;|HOURLYRelativeHumidity;|HOURLYWindSpeed.|
|----|----|----|----|----|----|
|0|01.01.2015 0:54|FM-15|22|50|10|
|1|01.01.2015 1:00|FM-12|22|50|10|
|2|01.01.2015 1:54|FM-15|22|50|10|
|3|01.01.2015 2:54|FM-15|22|50|11|

Предположим, что вам потребуется объединить этот файл с набором данных, в которых формат даты и времени — "10 марта 2018 г. 2:00–4:00".

```python
builder = dflow.builders.derive_column_by_example(
    source_columns=['DATE'], new_column_name='date_timerange')
builder.add_example(
    source_data=dflow.iloc[1], example_value='Jan 1, 2015 12AM-2AM')
builder.preview(count=5)
```

||DATE|date_timerange|
|----|----|----|
|0|01.01.2015 0:54|1 января 2015 г. 00:00–2:00|
|1|01.01.2015 1:00|1 января 2015 г. 00:00–2:00|
|2|01.01.2015 1:54|1 января 2015 г. 00:00–2:00|
|3|01.01.2015 2:54|1 января 2015 г. 2:00–4:00|
|4|01.01.2015 3:54|1 января 2015 г. 2:00–4:00|

Приведенный выше код сначала создает для производного столбца построитель. Вы указываете массив исходных столбцов, который учитывает (`DATE`) и имя нового столбца, который необходимо добавить. Как и в первом примере, вы переходите во вторую строку (индекс 1) и получаете ожидаемое значение для производного столбца.

Наконец, вы вызываете `builder.preview(skip=30, count=5)` и видите производный столбец рядом с исходным. Кажется, что формат правильный, но вы видите только значения той же даты "1 января 2015 г.".

Теперь укажите, сколько строк сверху вы хотите `skip`, чтобы увидеть строки ниже.

> [!NOTE]
> Функция предварительной версии () пропускает строки, но не выполняет повторную нумерацию выходного индекса. В приведенном ниже примере индекс 0 в таблице соответствует индексу 30 в потоке данных.

```python
builder.preview(skip=30, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|01.01.2015 22:54|1 января 2015 г. 22:00–00:00|
|1|01.01.2015 23:54|1 января 2015 г. 22:00–00:00|
|2|01.01.2015 23:59|1 января 2015 г. 22:00–00:00|
|3|02.01.2015 0:54|1 февраля 2015 г. 00:00–2:00|
|4|02.01.2015 1:00|1 февраля 2015 г. 00:00–2:00|

Здесь вы можете увидеть проблему со сгенерированной программой. Так как она основана исключительно на одном примере, который вы указали выше, программа выбрала формат синтаксической проверки даты "день/месяц/год", который не является тем, который вам нужен в этом варианте. Чтобы устранить эту проблему, выберите конкретный индекс записи и укажите другой пример с помощью `add_example()` функции `builder` для переменной.

```python
builder.add_example(
    source_data=dflow.iloc[3], example_value='Jan 2, 2015 12AM-2AM')
builder.preview(skip=30, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|01.01.2015 22:54|1 января 2015 г. 22:00–00:00|
|1|01.01.2015 23:54|1 января 2015 г. 22:00–00:00|
|2|01.01.2015 23:59|1 января 2015 г. 22:00–00:00|
|3|02.01.2015 0:54|2 января 2015 г. 12:00–2:00|
|4|02.01.2015 1:00|2 января 2015 г. 12:00–2:00|

Теперь строки правильно обрабатывали "1/2/2015" как "Янв 2, 2015", но если вы выйдете за пределы индекса 76 производного столбца, вы увидите, что в производном столбце отсутствуют значения.

```python
builder.preview(skip=75, count=5)
```


||DATE|date_timerange|
|-----|-----|-----|
|0|1/3/2015 7:00|3 января, 2015 06:00-8:00|
|1|1/3/2015 7:54|3 января, 2015 06:00-8:00|
|2|1/29/2015 6:54|Отсутствуют|
|3|1/29/2015 7:00|Отсутствуют|
|4|1/29/2015 7:54|Отсутствуют|

```python
builder.add_example(
    source_data=dflow.iloc[77], example_value='Jan 29, 2015 6AM-8AM')
builder.preview(skip=75, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/3/2015 7:00|3 января, 2015 06:00-8:00|
|1|1/3/2015 7:54|3 января, 2015 06:00-8:00|
|2|1/29/2015 6:54|29 января, 2015 06:00-8:00|
|3|1/29/2015 7:00|29 января, 2015 06:00-8:00|
|4|1/29/2015 7:54|29 января, 2015 06:00-8:00|

 Для просмотра списка текущих примеров вызовов `list_examples()` наследования для объекта построителя.

```python
examples = builder.list_examples()
```

| |DATE|пример|example_id|
| -------- | -------- | -------- | -------- |
|0|01.01.2015 1:00|1 января 2015 г. 00:00–2:00|-1|
|1|02.01.2015 0:54|2 января 2015 г. 12:00–2:00|-2|
|2|29.01.2015 20:54|29 января 2015 г. 20:00–22:00|-3|


В некоторых случаях, если необходимо удалить неправильные примеры, можно передать либо `example_row` из Pandas данных, либо `example_id` из значения. Например, при запуске `builder.delete_example(example_id=-1)`удаляется первый пример преобразования.


Вызовите `to_dataflow()` в построителе, который возвращает поток данных с добавленными выбранными производными столбцами.

```python
dflow = builder.to_dataflow()
df = dflow.to_pandas_dataframe()
```

## <a name="filtering"></a>Фильтрация

Пакет SDK включает методы [`drop_columns()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#drop-columns-columns--multicolumnselection-----azureml-dataprep-api-dataflow-dataflow) и [`filter()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py) позволяет отфильтровывать столбцы или строки.

### <a name="initial-setup"></a>Первоначальная настройка

> [!Note]
> URL-адрес в этом примере не является полным URL-адресом. Он ссылается на пример папки с в большом двоичном объекте. Полный URL-адрес данных https://dprepdata.blob.core.windows.net/demo/green-small/green_tripdata_2013-08.csv

В этом руководстве мы выполним загрузку всех файлов внутри папки и агрегирование результатов в green_df_raw и yellow_df_raw.

```python
import azureml.dataprep as dprep
from datetime import datetime
dflow = dprep.read_csv(
    path='https://dprepdata.blob.core.windows.net/demo/green-small/*')
dflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|store_and_fwd_flag|RateCodeID|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Отсутствуют|Отсутствуют|Отсутствуют|Отсутствуют|Отсутствуют|Отсутствуют|Отсутствуют|Отсутствуют|Отсутствуют|Отсутствуют|Отсутствуют|Отсутствуют|Отсутствуют|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|в|1|0|0|0|0|1|.00|0|0|21.25|
|2|2013-08-01 09:13:00|2013-08-01 11:38:00|в|1|0|0|0|0|2|.00|0|0|75|
|3|2013-08-01 09:48:00|2013-08-01 09:49:00|в|5|0|0|0|0|1|.00|0|1|2,1|
|4|2013-08-01 10:38:35|2013-08-01 10:38:51|в|1|0|0|0|0|1|.00|0|0|3.25|

### <a name="filtering-columns"></a>Фильтрация столбцов

Для фильтрации столбцов используйте `drop_columns()`. Этот метод принимает список столбцов для удаления или более сложного аргумента с именем [`ColumnSelector`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.columnselector?view=azure-dataprep-py).

#### <a name="filtering-columns-with-list-of-strings"></a>Фильтрация столбцов со списком строк

В этом примере `drop_columns()` использует список строк. Каждая строка должна точно соответствовать необходимому столбцу, который подлежит удалению.

```python
dflow = dflow.drop_columns(['Store_and_fwd_flag', 'RateCodeID'])
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Отсутствуют|Отсутствуют|Отсутствуют|Отсутствуют|Отсутствуют|Отсутствуют|Отсутствуют|Отсутствуют|Отсутствуют|Отсутствуют|Отсутствуют|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|0|0|0|0|1|.00|0|0|21.25|

#### <a name="filtering-columns-with-regex"></a>Фильтрация столбцов с регулярным выражением

Кроме того, для удаления столбцов, соответствующих регулярному выражению, можно использовать выражение `ColumnSelector`. В этом примере мы удаляем все столбцы, соответствующие выражению `Column*|.*longitude|.*latitude`.

```python
dflow = dflow.drop_columns(dprep.ColumnSelector(
    'Column*|.*longitud|.*latitude', True, True))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Отсутствуют|Отсутствуют|Отсутствуют|Отсутствуют|Отсутствуют|Отсутствуют|Отсутствуют|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|1|.00|0|0|21.25|

## <a name="filtering-rows"></a>Фильтрация строк

Для фильтрации строк используйте `filter()`. Этот метод принимает в качестве аргумента выражение пакета SDK для подготовки данных машинного обучения Azure и возвращает новый поток данных со строками, которые выражение оценивает как True. Выражения строятся с использованием построителя выражений (`col`, `f_not`, `f_and`, `f_or`) и обычных операторов (>, <, >=, <=, ==,!=).

### <a name="filtering-rows-with-simple-expressions"></a>Фильтрация строк с помощью простых выражений

С помощью построителя выражений `col` укажите имя столбца в качестве строкового аргумента `col('column_name')`. Это выражение можно использовать в сочетании с одним из стандартных операторов >, <, >=, <=, ==, != для создания такого выражения, как `col('Tip_amount') > 0`. Наконец, используйте созданные выражения в функции `filter()`.

В этом примере `dflow.filter(col('Tip_amount') > 0)` возвращает поток данных со строками, в которых значение `Tip_amount` больше 0.

> [!NOTE]
> `Tip_amount` сначала преобразуется в число, которое позволяет нам построить выражение, сравнивая его с другими числовыми значениями.

```python
dflow = dflow.to_number(['Tip_amount'])
dflow = dflow.filter(dprep.col('Tip_amount') > 0)
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-01 19:33:28|2013-08-01 19:35:21|5|.00|0.08|0|4.58|
|1|2013-08-05 13:16:38|2013-08-05 13:18:24|1|.00|0.30|0|3.8|

### <a name="filtering-rows-with-complex-expressions"></a>Фильтрация строк с помощью сложных выражений

Для фильтрации с помощью сложных выражений, объедините одно или несколько простых выражений с помощью построителя выражений `f_not`, `f_and`, или `f_or`.

В этом примере `dflow.filter()` возвращает поток данных со строками, в которых значение `'Passenger_count'` меньше 5, а значение `'Tolls_amount'` больше 0.

```python
dflow = dflow.to_number(['Passenger_count', 'Tolls_amount'])
dflow = dflow.filter(dprep.f_and(
    dprep.col('Passenger_count') < 5, dprep.col('Tolls_amount') > 0))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-08 12:16:00|2013-08-08 12:16:00|1.0|.00|2.25|5.00|19.75|
|1|2013-08-12 14:43:53|2013-08-12 15:04:50|1.0|5.28|6.46|5.33|32.29|

Вы также можете отфильтровать строки, объединив несколько построителей выражений и создав вложенное выражение.

> [!NOTE]
> `lpep_pickup_datetime` и `Lpep_dropoff_datetime` сначала преобразуются в дату и время (DateTime), что позволяет нам построить выражение, сравнивая его с другими значениями даты и времени (DateTime).

```python
dflow = dflow.to_datetime(
    ['lpep_pickup_datetime', 'Lpep_dropoff_datetime'], ['%Y-%m-%d %H:%M:%S'])
dflow = dflow.to_number(['Total_amount', 'Trip_distance'])
mid_2013 = datetime(2013, 7, 1)
dflow = dflow.filter(
    dprep.f_and(
        dprep.f_or(
            dprep.col('lpep_pickup_datetime') > mid_2013,
            dprep.col('Lpep_dropoff_datetime') > mid_2013),
        dprep.f_and(
            dprep.col('Total_amount') > 40,
            dprep.col('Trip_distance') < 10)))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-13 06:11:06+00:00|2013-08-13 06:30:28+00:00|1.0|9.57|7.47|5.33|44.80|
|1|2013-08-23 12:28:20+00:00|2013-08-23 12:50:28+00:00|2.0|8,22|8.08|5.33|40.41|

## <a name="custom-python-transforms"></a>Пользовательские преобразования Python

Всегда будут существовать сценарии, в которых написание собственного скрипта — самый простой вариант для создания преобразования. Пакет SDK предоставляет три разные расширения, которые можно применять для пользовательских скриптов на языке Python.

- Столбец нового скрипта
- Фильтр нового скрипта
- Преобразование секции

Каждое из расширений поддерживает вертикальное и горизонтальное масштабирование среды выполнения. Ключевым преимуществом использования этих точек расширения является то, что для создания кадра данных вам не требуется извлекать все данные. Ваш пользовательский код Python будет работать так же, как и другие преобразования, в масштабе, по разделам и, как правило, параллельно.

### <a name="initial-data-preparation"></a>Начальная подготовка данных

Начните с загрузки некоторых данных из хранилища Blob-объектов Azure.

```python
import azureml.dataprep as dprep
col = dprep.col

dflow = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv', skip_rows=1)
dflow.head(2)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|
|0|Алабама|1|101710|Округ Хейл|10171002158| |
|1|Алабама|1|101710|Округ Хейл|10171002162| |

Обрежьте набор данных и выполните некоторые базовые преобразования, включая удаление столбцов, замену значений и преобразование типов.

```python
dflow = dflow.keep_columns(
    ['stnam', 'leanm10', 'ncessch', 'MAM_MTH00numvalid_1011'])
dflow = dflow.replace_na(
    columns=['leanm10', 'MAM_MTH00numvalid_1011'], custom_na_list='.')
dflow = dflow.to_number(['ncessch', 'MAM_MTH00numvalid_1011'])
dflow.head(2)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|Алабама|Округ Хейл|1,017100e+10|Отсутствуют|
|1|Алабама|Округ Хейл|1,017100e+10|Отсутствуют|

С помощью указанного ниже фильтра найдите значения NULL.

```python
dflow.filter(col('MAM_MTH00numvalid_1011').is_null()).head(2)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|Алабама|Округ Хейл|1,017100e+10|Отсутствуют|
|1|Алабама|Округ Хейл|1,017100e+10|Отсутствуют|

### <a name="transform-partition"></a>Преобразование секции

Используйте [`transform_partition()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#transform-partition-script--str-----azureml-dataprep-api-dataflow-dataflow) для замены всех значений NULL значением 0. Этот код будет выполняться по секциям, но не во всем наборе данных одновременно. Это значит, что для большого набора данных этот код можно выполнять в параллельном режиме по мере выполнения обработки данных, то есть секция за секцией.

Скрипт Python должен определить функцию с именем `transform()`, которая принимает два аргумента: `df` и `index`. Аргумент `df` будет кадром данных pandas, содержащим данные для секции, а аргумент `index` — это уникальный идентификатор секции. Функция преобразования может полностью редактировать переданное в кадр данных, но должна возвращать кадр данных. Все библиотеки, которые импортирует скрипт Python, должны существовать в среде, где выполняется поток данных.

```python
df = df.transform_partition("""
def transform(df, index):
    df['MAM_MTH00numvalid_1011'].fillna(0,inplace=True)
    return df
""")
df.head(2)
```

||stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|Алабама|Округ Хейл|1,017100e+10|0,0|
|1|Алабама|Округ Хейл|1,017100e+10|0,0|

### <a name="new-script-column"></a>Столбец нового скрипта

С помощью скрипта Python можно создать новый столбец с названием района и названием штата, а также добавить в прописную букву имени состояния. Для этого используйте [`new_script_column()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#new-script-column-new-column-name--str--insert-after--str--script--str-----azureml-dataprep-api-dataflow-dataflow) метод в потоке данных.

Скрипт Python должен определить функцию с именем `newvalue()`, которая принимает один аргумент: `row`. Аргумент `row` — это словарь (`key`: имя столбца, `val`: текущее значение), он передается в эту функцию для каждой строки в наборе данных. Эта функция должна возвращать значение, используемое в новом столбце. Все библиотеки, которые импортирует скрипт Python, должны существовать в среде, где выполняется поток данных.

```python
dflow = dflow.new_script_column(new_column_name='county_state', insert_after='leanm10', script="""
def newvalue(row):
    return row['leanm10'] + ', ' + row['stnam'].title()
""")
dflow.head(2)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|Алабама|Округ Хейл|Округ Хейл, штат Алабама|1,017100e+10|0,0|
|1|Алабама|Округ Хейл|Округ Хейл, штат Алабама|1,017100e+10|0,0|

### <a name="new-script-filter"></a>Фильтр нового скрипта

Создайте выражение Python, используя [`new_script_filter()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#new-script-filter-script--str-----azureml-dataprep-api-dataflow-dataflow) , чтобы отфильтровать набор данных только по строкам, где "Хале" не `county_state` находится в новом столбце. Выражение возвращает `True`, если строку можно сохранить, и `False`, если ее нужно удалить.

```python
dflow = dflow.new_script_filter("""
def includerow(row):
    val = row['county_state']
    return 'Hale' not in val
""")
dflow.head(2)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|Алабама|Округ Джефферсон|Округ Джефферсон, штат Алабама|1.019200e+10|1.0|
|1|Алабама|Округ Джефферсон|Округ Джефферсон, штат Алабама|1.019200e+10|0,0|

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения см. в [справочной документации](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#dataprep) по пакету SDK для подготовки данных машинное обучение Azure.
