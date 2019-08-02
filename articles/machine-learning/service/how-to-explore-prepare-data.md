---
title: Просмотр и преобразование данных (класс DataSet)
titleSuffix: Azure Machine Learning service
description: Просмотр данных с помощью сводных статистических данных и подготовка данных с помощью очистки, преобразования и проектирования характеристик
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/23/2019
ms.openlocfilehash: f680a1cb15edf0141897c74da3b7c7afa01acae0
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699122"
---
# <a name="explore-and-prepare-data-with-the-dataset-class-preview"></a>Просмотр и подготовка данных с помощью класса DataSet (Предварительная версия)

Узнайте, как исследовать и подготавливать данные с помощью пакета azureml-наборы данных в пакете [SDK для машинное обучение Azure](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Класс [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) (Предварительная версия) позволяет исследовать и подготавливать данные, предоставляя такие функции, как выборка, сводная статистика и интеллектуальные преобразования. Шаги преобразования сохраняются в [определениях наборов данных](how-to-manage-dataset-definitions.md) с возможностью обработки нескольких больших файлов различных схем с высоким уровнем масштабируемости.

> [!Important]
> Некоторые классы набора данных (Предварительная версия) имеют зависимости от пакета [azureml-](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) DataMarket. Хотя функции преобразования можно выполнять непосредственно с помощью [функций подготовки данных](how-to-transform-data.md)га'ед, мы рекомендуем использовать оболочки пакетов набора данных, описанные в этой статье, если вы создаете новое решение. Машинное обучение Azure наборы данных (Предварительная версия) позволяют не только преобразовывать данные, но и [данные моментального снимка](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py) и сохранять [определения набора DataSet с управлением версиями](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py). Наборы данных — это следующая версия пакета SDK для подготовки к данным, предлагающая расширенные функциональные возможности для управления DataSet-наборами в решениях искусственного интеллекта.

## <a name="prerequisites"></a>предварительные требования

Для изучения и подготовки данных потребуется:

* Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).

* Рабочая область службы машинного обучения Azure. См. раздел [Создание рабочей области службы машинное обучение Azure](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* Пакет SDK Машинное обучение Azure для Python (версия 1.0.21 или более поздняя), включающий пакет azureml-DataSets. Чтобы установить или обновить последнюю версию пакета SDK, см. статью [Установка или обновление пакета SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* Машинное обучение Azure пакет SDK для подготовки данных. Чтобы установить или обновить последнюю версию, см. статью [Установка или обновление пакета SDK для подготовки данных](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py#install).

* Скачайте примеры файлов, чтобы перейти к следующим примерам: [преступления. csv](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) и [City. JSON](https://dprepdata.blob.core.windows.net/dataset-sample-files/city.json).

## <a name="sampling"></a>Выборка

Ознакомьтесь с примерами данных, чтобы получить начальное представление об архитектуре и содержимом данных. В настоящее время [`sample()`](https://docs.microsoft.com//python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#sample-sample-strategy--arguments-) метод из класса DataSet поддерживает стратегии выборки "первые N", "простые случайные" и "стратифицированной".

```Python
from azureml.core.dataset import Dataset
import random

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')

# create seed for Simple Random and Stratified sampling
seed = random.randint(0, 4294967295)
```

### <a name="top-n-sample"></a>N основных примеров

Для N основных выборок в качестве примера используется первая n записей набора данных. Это полезно, если вы только пытаетесь получить представление о том, как выглядят записи данных, или узнать, какие поля находятся в данных.

```Python
top_n_sample_dataset = dataset.sample('top_n', {'n': 5})
top_n_sample_dataset.to_pandas_dataframe()
```

||id|Серийный номер|Дата|Заблокировать|IUCR|Основной тип|...|
-|--|-----------|----|-----|----|------------|---
0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH|1153|ПРАКТИКА|...
1|10516598|HZ258664|4/15/2016 17:00|082XX МАРШФИЕЛД AVE|890|КРАЖА|...
2|10519196|HZ261252|4/15/2016 10:00|104XX САКРАМЕНТО AVE|1154|ПРАКТИКА|...
3|10519591|HZ261534|4/15/2016 9:00|113XX ЗАРАНЕЕ AVE|1120|ПРАКТИКА|...
4|10534446|HZ277630|4/15/2016 10:00|055XX N КЕДЗИЕ AVE|890|КРАЖА|...

### <a name="simple-random-sample"></a>Простая случайная выборка

В простой случайной выборки каждый член заполнения данных имеет равную вероятность выбора в качестве части примера. `simple_random` В примере стратегии записи из набора данных выбираются на основе указанной вероятности и возвращает измененный набор данных. Параметр SEED является необязательным.

```Python
simple_random_sample_dataset = dataset.sample('simple_random', {'probability':0.3, 'seed': seed})
simple_random_sample_dataset.to_pandas_dataframe()
```

||id|Серийный номер|Дата|Заблокировать|IUCR|Основной тип|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX МАРШФИЕЛД AVE|890|КРАЖА|...
1|10519196|HZ261252|4/15/2016 10:00|104XX САКРАМЕНТО AVE|1154|ПРАКТИКА|...
2|10534446|HZ277630|4/15/2016 10:00|055XX N КЕДЗИЕ AVE|890|КРАЖА|...

### <a name="stratified-sample"></a>Пример стратифицированной

Примеры стратифицированной гарантируют, что определенные группы заполнения представлены в примере. В примере стратегии заполнение делится на Strata, или на подгруппы на основе сходства, а записи выбираются случайным образом из каждого Strata в соответствии с весовыми коэффициентами Strata, указанными в `fractions` параметре. `stratified`

В следующем примере каждая запись группируются по указанным столбцам и включает произнесенную запись на основе сведений о весе Strata X в `fractions`. Если Strata не указан или запись не может быть сгруппирована, весовым коэффициентом по умолчанию для выборки является 0.

```Python
# take 50% of records with `Primary Type` as `THEFT` and 20% of records with `Primary Type` as `DECEPTIVE PRACTICE` into sample Dataset
fractions = {}
fractions[('THEFT',)] = 0.5
fractions[('DECEPTIVE PRACTICE',)] = 0.2

sample_dataset = dataset.sample('stratified', {'columns': ['Primary Type'], 'fractions': fractions, 'seed': seed})

sample_dataset.to_pandas_dataframe()
```

||id|Серийный номер|Дата|Заблокировать|IUCR|Основной тип|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX МАРШФИЕЛД AVE|890|КРАЖА|...
1|10534446|HZ277630|4/15/2016 10:00|055XX N КЕДЗИЕ AVE|890|КРАЖА|...
2|10535059|HZ278872|4/15/2016 4:30|004XX КИЛБАУРН AVE|810|КРАЖА|...

## <a name="explore-with-summary-statistics"></a>Просмотр сводных статистических сведений

 Выявлять аномалии, отсутствующие значения или счетчики ошибок [`get_profile()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) с помощью метода. Эта функция получает профиль и сводную статистику данных, которая, в свою очередь, помогает определить необходимые операции подготовки данных для применения.

```Python
dataset.get_profile()
```

||Тип|Min|Max|Count|Отсутствующее количество|Присутствующее количество|Процент отсутствующих|Количество ошибок|Количество пустых|0,1 % квантилей|1 % квантилей|5 % квантилей|25 % квантилей|50 % квантилей|75 % квантилей|95 % квантилей|99 % квантилей|99,9 % квантилей|Среднее значение|Стандартное отклонение|Variance|Асимметрия|Эксцесс
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType. INTEGER|1.04986 e + 07|1.05351 e + 07|10,0|0,0|10,0|0,0|0,0|0,0|1.04986 e + 07|1.04992 e + 07|1.04986 e + 07|1.05166 e + 07|1.05209 e + 07|1.05259e+07|1.05351 e + 07|1.05351 e + 07|1.05351 e + 07|1.05195 e + 07|12302,7|1.51358e+08|— 0,495701|— 1,02814
Серийный номер|FieldType.STRING|HZ239907|HZ278872|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
Дата|FieldType. Дата|2016-04-04 23:56:00 + 00:00|2016-04-15 17:00:00 + 00:00|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
Заблокировать|FieldType.STRING|004XX КИЛБАУРН AVE|113XX ЗАРАНЕЕ AVE|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
IUCR|FieldType. INTEGER|810|1154|10,0|0,0|10,0|0,0|0,0|0,0|810|850|810|890|1136|1153|1154|1154|1154|1058,5|137,285|18847,2|— 0,785501|— 1,3543
Основной тип|FieldType.STRING|ПРАКТИКА|КРАЖА|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
Описание|FieldType.STRING|ПОДДЕЛЬНАЯ ПРОВЕРКА|СВЫШЕ $500|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
Описание расположения|FieldType.STRING||УЧЕБНЫЙ, ОБЩЕДОСТУПНЫЙ, ЗДАНИЕ|10,0|0,0|10,0|0,0|0,0|1.0||||||||||||||
Фикс.|FieldType. BOOLEAN|False|False|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
Дом|FieldType. BOOLEAN|False|False|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
Пульс|FieldType. INTEGER|531|2433|10,0|0,0|10,0|0,0|0,0|0,0|531|531|531|614|1318,5|1911|2433|2433|2433|1371,1|692,094|478994|0,105418|— 1,60684
Округ|FieldType. INTEGER|5|24|10,0|0,0|10,0|0,0|0,0|0,0|5|5|5|6|13|19|24|24|24|13,5|6,94822|48,2778|0,0930109|— 1,62325
Административный район|FieldType. INTEGER|1|48|10,0|0,0|10,0|0,0|0,0|0,0|1|5|1|9|22,5|40|48|48|48|24,5|16,2635|264,5|0,173723|— 1,51271
Жилой микрорайон|FieldType. INTEGER|4|77|10,0|0,0|10,0|0,0|0,0|0,0|4|8.5|4|24|37,5|71|77|77|77|41,2|26,6366|709,511|0,112157|— 1,73379
Код ФБР|FieldType. INTEGER|6|11|10,0|0,0|10,0|0,0|0,0|0,0|6|6|6|6|11|11|11|11|11|9.4|2,36643|5.6|— 0,702685|— 1,59582
Координата X|FieldType. INTEGER|1.16309e+06|1.18336 e + 06|10,0|7.0|3.0|0,7|0,0|0,0|1.16309e+06|1.16309e+06|1.16309e+06|1.16401 e + 06|1.16678e+06|1.17921e+06|1.18336 e + 06|1.18336 e + 06|1.18336 e + 06|1.17108 e + 06|10793,5|1.165e+08|0,335126|— 2,33333
Координата Y|FieldType. INTEGER|1.8315 e + 06|1.908 e + 06|10,0|7.0|3.0|0,7|0,0|0,0|1.8315 e + 06|1.8315 e + 06|1.8315 e + 06|1.83614 e + 06|1.85005 e + 06|1.89352e+06|1.908 e + 06|1.908 e + 06|1.908 e + 06|1.86319 e + 06|39905,2|1.59243e+09|0,293465|— 2,33333
Год|FieldType. INTEGER|2016|2016|10,0|0,0|10,0|0,0|0,0|0,0|2016|2016|2016|2016|2016|2016|2016|2016|2016|2016|0|0|NaN|NaN
Обновлено|FieldType. Дата|2016-05-11 15:48:00 + 00:00|2016-05-27 15:45:00 + 00:00|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
Широта|FieldType.DECIMAL|41,6928|41,9032|10,0|7.0|3.0|0,7|0,0|0,0|41,6928|41,6928|41,6928|41,7057|41,7441|41,8634|41,9032|41,9032|41,9032|41,78|0,109695|0,012033|0,292478|— 2,33333
Долгота|FieldType.DECIMAL|— 87,6764|— 87,6043|10,0|7.0|3.0|0,7|0,0|0,0|— 87,6764|— 87,6764|— 87,6764|— 87,6734|— 87,6645|— 87,6194|— 87,6043|— 87,6043|— 87,6043|— 87,6484|0,0386264|0,001492|0,344429|— 2,33333
Местоположение|FieldType.STRING||(41,903206037,-87,676361925)|10,0|0,0|10,0|0,0|0,0|7.0||||||||||||||

## <a name="impute-missing-values"></a>Добавление отсутствующих значений

В наборах данных значения NULL, значения NaN и не содержат содержимого считаются отсутствующими значениями. Это может повлиять на производительность моделей машинного обучения или привести к недопустимым выводам. Добавления отсутствующих является одним из распространенных подходов к устранению отсутствующих значений и полезен при наличии большого процента от отсутствующих записей значений, которые нельзя просто удалить.

В профиле набора данных, созданном в предыдущем разделе, видно `Latitude` , `Longitude` что столбцы и имеют высокий процент отсутствующих значений. В этом примере вычисляется среднее и аппроксимация недостающие значения для этих двух столбцов.

Сначала получите Последнее определение набора данных с помощью [`get_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-definition-version-id-none-) и немного очистить данные с помощью [`keep_columns()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#keep-columns-columns--multicolumnselection--validate-column-exists--bool---false-----azureml-dataprep-api-dataflow-dataflow), поэтому мы видим только те столбцы, которые нам нужно устранить.

```Python
from azureml.core.dataset import Dataset
import azureml.dataprep as dprep

# get the latest definition of Dataset
ds_def = dataset.get_definition()

# keep useful columns for this example
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
ds_def.head(3)
```

||id|Фикс.| Широта|Долгота|
-|---------|-----|---------|----------|
|0|10498554|False|41,692834|— 87,604319|
|1|10516598|False| 41,744107 |— 87,664494|
|2|10519196|False| NaN|NaN|

Затем проверьте `MEAN` значение столбца широты [`summarize()`](/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-ml-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow--summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) с помощью функции. Эта функция принимает массив столбцов в параметре `group_by_columns`, чтобы указать совокупный уровень. Параметр принимает функцию, которая задает имя текущего столбца, имя нового вычисляемого поля и выполняемый объект `SummaryFunction`. `summary_columns` `SummaryColumnsValue`

```Python
lat_mean = ds_def.summarize(group_by_columns = ['Arrest'],
                            summary_columns = [dprep.SummaryColumnsValue(column_id = 'Latitude',
                                                                         summary_column_name = 'Latitude_MEAN',
                                                                         summary_function = dprep.SummaryFunction.MEAN)])
lat_mean = lat_mean.filter(lat_mean['Arrest'] == False)
lat_mean.head(1)
```

||Фикс.|Latitude_MEAN|
--|-----|--------|
|0|False|41,780049|

После проверки значений на аппроксимация используйте [`ImputeMissingValuesBuilder`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) , чтобы изучить фиксированное выражение, импутес столбцы с `MAX`вычисляемыми `MIN`значениями,, `MEAN` значениями или `CUSTOM` значениями. Если указано `group_by_columns`, то отсутствующие значения будут добавляться группой вместе с вычисленными `MIN`, `MAX` и `MEAN` для каждой группы.

Принимает column_id строку `ReplaceValueFunction` и, чтобы указать тип аппроксимация. [`ImputeColumnArguments`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputecolumnarguments?view=azure-dataprep-pyfunction) Для отсутствующего значения долготы аппроксимация его с-87 на основе внешних знаний.

```Python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id = 'Latitude',
                                          impute_function = dprep.ReplaceValueFunction.MEAN)

# impute with custom value -87
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=-87)
```

Шаги аппроксимация можно объединить в `ImputeMissingValuesBuilder` объект [`Builders`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py) с помощью функции [`impute_missing_values()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py#impute-missing-values-impute-columns--typing-list-azureml-dataprep-api-builders-imputecolumnarguments----none--group-by-columns--typing-union-typing-list-str---nonetype----none-----azureml-dataprep-api-builders-imputemissingvaluesbuilder)класса. Параметр `impute_columns` принимает массив объектов `ImputeColumnArguments`.

```Python
# get instance of ImputeMissingValuesBuilder
impute_builder = ds_def.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
```

Вызовите [`to_dataflow()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#to-dataflow------azureml-dataprep-api-dataflow-dataflow)функцию, чтобы сохранить шаги аппроксимация и применить их к объекту потока данных с помощью. [`learn()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#learn------none)

```Python
impute_builder.learn()
ds_def = impute_builder.to_dataflow()
ds_def.head(3)
```

Как показано в следующей выходной таблице, недостающая Широта была добавленные со `MEAN` `Arrest==False` значением Group, а недостающая Долгота — добавленные с-87.

||id|Фикс.|Широта|Долгота
-|---------|-----|---------|----------
0|10498554|False|41,692834|— 87,604319
1|10516598|False|41,744107|— 87,664494
2|10519196|False|41,780049|— 87,000000

Обновите определение набора данных с [`update_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#update-definition-definition--definition-update-message-) помощью, чтобы избежать выполненных действий преобразования.

```Python
dataset = dataset.update_definition(ds_def, 'Impute Missing')
dataset.head(3)
```

||id|Фикс.|Широта|Долгота
-|---------|-----|---------|----------
0|10498554|False|41,692834|— 87,604319
1|10516598|False|41,744107|— 87,664494
2|10519196|False|41,780049|— 87,000000

## <a name="create-assertion-rules"></a>Создание правил утверждения

Часто данные, с которыми мы работаем при очистке и подготовке данных, являются лишь подмножеством всего объема данных, необходимых для рабочей среды. В результате некоторые предположения, которые мы делаем в ходе чистки, могут быть ложны. Например, в наборе данных, который обновляется непрерывно, столбец, изначально содержащий только числа в определенном диапазоне, может содержать более широкий диапазон значений при последующих выполнениях. Эти ошибки часто приводят к поврежденным конвейерам или неправильным данным.

Наборы данных поддерживают создание утверждений на данные, которые оцениваются по мере выполнения конвейера. Эти утверждения позволяют убедиться в том, что наши предположения о данных продолжают быть точными и, если нет, для устранения ошибок соответствующим образом.

Например, если нужно ограничить `Latitude` и `Longitude` значения в наборе данных до [`assert_value()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#assert-value-columns--multicolumnselection--expression--azureml-dataprep-api-expressions-expression--policy--azureml-dataprep-api-engineapi-typedefinitions-assertpolicy----assertpolicy-errorvalue--1---error-code--str----assertionfailed------azureml-dataprep-api-dataflow-dataflow) конкретных числовых диапазонов, метод гарантирует, что это всегда так.

```Python
from azureml.dataprep import value
from azureml.core.dataset import Dataset

# get the latest definition of the Dataset
ds_def = dataset.get_definition()

# set assertion rules for `Latitude` and `Longitude` columns
ds_def = ds_def.assert_value('Latitude', (value <= 90) & (value >= -90), error_code='InvalidLatitude')
ds_def = ds_def.assert_value('Longitude', (value <= 180) & (value >= -87), error_code='InvalidLongitude')

ds_def.get_profile()
```

||Type|Min|Max|Count|Отсутствующее количество|Присутствующее количество|Процент отсутствующих|Количество ошибок|Количество пустых|0,1 % квантилей|1 % квантилей|5 % квантилей|25 % квантилей|50 % квантилей|75 % квантилей|95 % квантилей|99 % квантилей|99,9 % квантилей|Среднее значение|Стандартное отклонение|Variance|Асимметрия|Эксцесс
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType. INTEGER|1.04986 e + 07|1.05351 e + 07|10,0|0,0|10,0|0,0|0,0|0,0|1.04986 e + 07|1.04992 e + 07|1.04986 e + 07|1.05166 e + 07|1.05209 e + 07|1.05259e+07|1.05351 e + 07|1.05351 e + 07|1.05351 e + 07|1.05195 e + 07|12302,7|1.51358e+08|— 0,495701|— 1,02814
Фикс.|FieldType. BOOLEAN|False|False|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
Широта|FieldType.DECIMAL|41,6928|41,9032|10,0|0,0|10,0|0,0|0,0|0,0|41,6928|41,7185|41,6928|41,78|41,78|41,78|41,9032|41,9032|41,9032|41,78|0,0517107|0,002674|0,837593|1.05
Долгота|FieldType. INTEGER|— 87|— 87|10,0|0,0|10,0|0,0|3.0|0,0|— 87|— 87|— 87|— 87|— 87|— 87|— 87|— 87|— 87|— 87|0|0|NaN|NaN

В профиле можно увидеть, что `Error Count` `Longitude` для столбца задано значение 3. Следующий код фильтрует набор данных, получает ошибку и видит, какое значение приводит к сбою утверждения. Здесь можно настроить код и очистить данные соответствующим образом.

```Python
from azureml.dataprep import col

ds_error = ds_def.filter(col('Longitude').is_error())
error = ds_error.head(10)['Longitude'][0]

print(error.originalValue)
```

    -87.60431945

## <a name="derive-columns-by-example"></a>Получение столбцов по образцу

Одним из более сложных средств для наборов данных является возможность получения столбцов с помощью примеров требуемых результатов. Это позволяет получить пример пакета SDK, чтобы он мог создать код для достижения предполагаемых преобразований.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')
dataset.head(3)
```

||id|Серийный номер|Дата|Заблокировать|...|
-|---------|-----|---------|----|---
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH|...
1|10516598|HZ258664|2016-04-15 17:00:00|082XX МАРШФИЕЛД AVE|...
2|10519196|HZ261252|2016-04-15 10:00:00|104XX САКРАМЕНТО AVE|...

Предположим, необходимо преобразовать формат даты и времени в значение "2016-04-04 10PM-12AM". В аргументе укажите примеры желаемого результата `example_data` в параметре в следующем формате: *(исходные выходные данные, требуемые выходные данные)* . [`derive_column_by_example()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#derive-column-by-example-source-columns--sourcecolumns--new-column-name--str--example-data--exampledata-----azureml-dataprep-api-dataflow-dataflow)

Следующий код предоставляет два примера желаемых выходных данных: ("2016-04-04 23:56:00", "2016-04-04 10PM-12AM") и ("2016-04-15 17:00:00", "2016-04-15 16:00-18:00").

```Python
ds_def = dataset.get_definition()
ds_def = ds_def.derive_column_by_example(
        source_columns = "Date",
        new_column_name = "Date_Time_Range",
        example_data = [("2016-04-04 23:56:00", "2016-04-04 10PM-12AM"), ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")]
    )
ds_def.keep_columns(['ID','Date','Date_Time_Range']).head(3)
```

Обратите внимание, что в следующей таблице новый столбец Date_Time_Range содержит записи в указанном формате.

||id|Дата|Date_Time_Range
-|--------|-----|----
0|10498554|2016-04-04 23:56:00|2016-04-04 10PM — 12AM
1|10516598|2016-04-15 17:00:00|2016-04-15 16:00 — 18:00
2|10519196|2016-04-15 10:00:00|2016-04-15 10:00 — 0:00

```Python
# update Dataset definition to keep the transformation steps performed.
dataset = dataset.update_definition(ds_def, 'Derive Date_Time_Range')
```

## <a name="fuzzy-groupings"></a>Нечеткие группирования

При сборе данных из разных источников могут возникнуть различия в написании, капитализации или сокращении одних и тех же сущностей. Автоматическая стандартизация и согласование этих вариантов с помощью нечеткого группирования пакета SDK или функции кластеризации текста.

Например, столбец `inspections.business.city` содержит несколько форм названия города San Франциско.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local json file
dataset = Dataset.auto_read_files('./data/city.json')
dataset.head(5)
```

||проверки. Business. business_id|проверки. business_name|проверки. бизнес. адрес|проверки. бизнес. City|...|
-|-----|-------------------------|------------|--|---
0|16162|Quick-N-Ези индийский продуктов|3861 24|SF|...
1|67565|Короля тайского лапша кафе|1541 ТАРАВАЛ|САН — ФРАНЦИСКО|...
2|67565|Короля тайского лапша кафе|1541 ТАРАВАЛ|САН — ФРАНЦИСКО|...
3|68701|гриндз|832 Клемент|SF|...
4|69186|Премьер адаптируются & Events, Inc.|1255 22|С.Ф.|...

Мы используем [`fuzzy_group_column()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#fuzzy-group-column-source-column--str--new-column-name--str--similarity-threshold--float---0-8--similarity-score-column-name--str---none-----azureml-dataprep-api-dataflow-dataflow) метод, чтобы добавить столбец с автоматически обнаруженной канонической формой "Сан-Франциско". Аргументы `source_column` и`new_column_name` являются обязательными. У вас также есть возможность:

* Создайте новый столбец `similarity_score_column_name`, который показывает показатель подобия между каждой парой исходных и канонических значений.

* Укажите значение `similarity_threshold`, которое является минимальным показателем подобия значений, сгруппированных вместе.

```Python
# get the latest Dataset definition
ds_def = dataset.get_definition()
ds_def = ds_def.fuzzy_group_column(source_column='inspections.business.city',
                                       new_column_name='city_grouped',
                                       similarity_threshold=0.8,
                                       similarity_score_column_name='similarity_score')
ds_def.head(5)
```

||проверки. Business. business_id|проверки. business_name|проверки. бизнес. адрес|проверки. бизнес. City|city_grouped|similarity_score|...|
-|-----|-------------------------|------------|--|---|---|---
0|16162|Quick-N-Ези индийский продуктов|3861 24|SF|Сан — Франциско|0,814806|...
1|67565|Короля тайского лапша кафе|1541 ТАРАВАЛ|САН — ФРАНЦИСКО|Сан — Франциско|1,000000|...
2|67565|Короля тайского лапша кафе|1541 ТАРАВАЛ|САН — ФРАНЦИСКО|Сан — Франциско|1,000000|...
3|68701|гриндз|832 Клемент|SF|Сан — Франциско|0,814806|...
4|69186|Премьер адаптируются & Events, Inc.|1255 22|С.Ф.|Сан — Франциско|0,814806|...

В результирующем определении набора данных все различные варианты представления "Сан-Франциско" в данных были нормализованы до одной и той же строки "Сан-Франциско".

Сохраните этот шаг нечеткого группирования в последнем определении набора `update_definition()`данных с помощью.

```Python
dataset = dataset.update_definition(ds_def, 'fuzzy grouping')
```

## <a name="next-steps"></a>Следующие шаги

* Пример модели регрессии см. в [руководстве](tutorial-auto-train-models.md) по автоматизированному машинному обучению.

* См. [Обзор](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) SDK по шаблонам и примерам использования.

* Пример использования наборов данных см. в [примере записных книжек](https://aka.ms/dataset-tutorial).
