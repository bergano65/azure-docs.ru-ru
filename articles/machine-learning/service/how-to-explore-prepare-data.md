---
title: Изучение и преобразование данных (класс набора данных)
titleSuffix: Azure Machine Learning service
description: Просмотр данных с помощью сводные статистические данные и Подготовка данных с помощью очистки данных, преобразования и проектирование признаков
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/23/2019
ms.openlocfilehash: e29ef2616a43223ec582575ca6363f78b26e5f22
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66753058"
---
# <a name="explore-and-prepare-data-with-the-dataset-class-preview"></a>Изучение и Подготовка данных с помощью класса набора данных (Предварительная версия)

Узнайте, как изучение и Подготовка данных с помощью пакета наборы данных машинного обучения Azure в [пакета SDK для Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). [Набора данных](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) класс (Предварительная версия) позволяет изучить и подготовить данные, предоставляя функции, такие как: выборки, сводные статистические данные и интеллектуальный преобразований. Действия преобразования сохраняются в [определения наборов данных](how-to-manage-dataset-definitions.md) возможность высокой степенью масштабируемости образом обрабатывать несколько больших файлов, разных схемах.

> [!Important]
> Некоторые классы наборов данных (Предварительная версия) имеют зависимости от [azureml dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) пакета (GA). Хотя функции преобразования можно сделать непосредственно с GA'ed [функции подготовки данных](how-to-transform-data.md), мы рекомендуем оболочки пакета набора данных, описанные в этой статье, при создании нового решения. Azure наборы данных обучения машины (Предварительная версия) позволяют не только преобразовывать данные, но также [данных моментального снимка](how-to-create-dataset-snapshots.md) и сохранить [определения наборов данных с версиями](how-to-manage-dataset-definitions.md). Наборы данных — следующая версия пакета SDK для подготовки данных, предлагая расширенными функциональными возможностями для управления наборами данных в решения искусственного Интеллекта.

## <a name="prerequisites"></a>Технические условия

Чтобы изучить и подготовить данные, вам потребуется:

* Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).

* Рабочая область службы машинного обучения Azure. См. в разделе [создать рабочую область службы машинного обучения Azure](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* Azure Machine Learning SDK для Python (версию 1.0.21 или более поздней версии), который включает пакет наборы данных машинного обучения Azure. Для установки или обновления до последней версии пакета SDK, см. в разделе [установить или обновить пакет SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* Подготовка данных Azure Machine Learning SDK. Для установки или обновления до последней версии, см. в разделе [установить или обновить пакет SDK для подготовки данных](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py#install).

* Скачайте файлы образца для изучения примеров: [crime.csv](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) и [city.json](https://dprepdata.blob.core.windows.net/dataset-sample-files/city.json).

## <a name="sampling"></a>Выборка

Использовать образец данных, чтобы разобраться в первоначальной архитектуры данных и содержимого. В настоящее время [ `sample()` ](https://docs.microsoft.com//python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#sample-sample-strategy--arguments-) стратегий выборки Top N случайных простой и Stratified поддерживает метод из класса набора данных.

```Python
from azureml.core.dataset import Dataset
import random

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')

# create seed for Simple Random and Stratified sampling
seed = random.randint(0, 4294967295)
```

### <a name="top-n-sample"></a>Пример первые N

Для выборки Top N первых n записей из набора данных, которые вашего примера. Это может оказаться полезным, если только вы пытаетесь получить представление о какой интерфейс записи данных как или, чтобы увидеть поля, имеющиеся в данных.

```Python
top_n_sample_dataset = dataset.sample('top_n', {'n': 5})
top_n_sample_dataset.to_pandas_dataframe()
```

||ИД|Серийный номер|Дата|Блок|IUCR|Основной тип|...|
-|--|-----------|----|-----|----|------------|---
0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|МОШЕННИЧЕСКИЕ ПРАКТИКЕ|...
1|10516598|HZ258664|4/15/2016 17:00|082XX С MARSHFIELD ОХРАНИТЬ|890|КРАЖА|...
2|10519196|HZ261252|4/15/2016 10:00|САКРАМЕНТО С 104XX ОХРАНИТЬ|1154|МОШЕННИЧЕСКИЕ ПРАКТИКЕ|...
3|10519591|HZ261534|4/15/2016 9:00|113XX С НАПУГАННЫМ ОХРАНИТЬ|1120|МОШЕННИЧЕСКИЕ ПРАКТИКЕ|...
4\.|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|КРАЖА|...

### <a name="simple-random-sample"></a>Простой Случайная выборка

В простой случайной выборки, каждый член совокупности данных с одинаковой вероятностью выбора как часть образца. В `simple_random` образец стратегии записей из набора данных выбираются на основе вероятности указан и возвращает измененный набор данных. Параметр начального значения является необязательным.

```Python
simple_random_sample_dataset = dataset.sample('simple_random', {'probability':0.3, 'seed': seed})
simple_random_sample_dataset.to_pandas_dataframe()
```

||ИД|Серийный номер|Дата|Блок|IUCR|Основной тип|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX С MARSHFIELD ОХРАНИТЬ|890|КРАЖА|...
1|10519196|HZ261252|4/15/2016 10:00|САКРАМЕНТО С 104XX ОХРАНИТЬ|1154|МОШЕННИЧЕСКИЕ ПРАКТИКЕ|...
2|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|КРАЖА|...

### <a name="stratified-sample"></a>Стратифицированной выборки

Стратифицированное примеры убедитесь, что в нем представлены определенных групп из совокупности значений. В `stratified` примера стратегии заполнение состоит из представленных стратификации или подгруппы, на основании сходства, и записи выбираются в случайном порядке из каждого слоя в соответствии с стратификации весовые коэффициенты, обозначается `fractions` параметра.

В следующем примере мы группе каждой записи по указанным столбцам и включить эти записи на информацию об объеме стратификации X в основе `fractions`. Если стратификации не указан или записи не могут быть сгруппированы, по умолчанию вес пример равен 0.

```Python
# take 50% of records with `Primary Type` as `THEFT` and 20% of records with `Primary Type` as `DECEPTIVE PRACTICE` into sample Dataset
fractions = {}
fractions[('THEFT',)] = 0.5
fractions[('DECEPTIVE PRACTICE',)] = 0.2

sample_dataset = dataset.sample('stratified', {'columns': ['Primary Type'], 'fractions': fractions, 'seed': seed})

sample_dataset.to_pandas_dataframe()
```

||ИД|Серийный номер|Дата|Блок|IUCR|Основной тип|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX С MARSHFIELD ОХРАНИТЬ|890|КРАЖА|...
1|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|КРАЖА|...
2|10535059|HZ278872|4/15/2016 4:30|004XX С KILBOURN ОХРАНИТЬ|810|КРАЖА|...

## <a name="explore-with-summary-statistics"></a>Произвольное тестирование с сводные статистические данные

 Обнаруживать аномалии, отсутствующие значения, или число ошибок с [ `get_profile()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) метод. Эта функция получает профиль и сводную статистику данных, которая позволит определить, операции подготовки необходимых данных для применения.

```Python
dataset.get_profile()
```

||type|Min|Max|Количество|Отсутствующее количество|Присутствующее количество|Процент отсутствующих|Количество ошибок|Количество пустых|0,1 % квантилей|1 % квантилей|5 % квантилей|25 % квантилей|50 % квантилей|75 % квантилей|95 % квантилей|99 % квантилей|99,9 % квантилей|Среднее значение|стандартное отклонение.|Variance|Асимметрия|Эксцесс
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ИД|FieldType.INTEGER|1.04986e+07|1.05351e+07|10,0|0,0|10,0|0,0|0,0|0,0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e+07|1.05259e+07|1.05351e+07|1.05351e+07|1.05351e+07|1.05195e+07|12302.7|1.51358e+08|-0.495701|-1.02814
Серийный номер|FieldType.STRING|HZ239907|HZ278872|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
Дата|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
Блок|FieldType.STRING|004XX С KILBOURN ОХРАНИТЬ|113XX С НАПУГАННЫМ ОХРАНИТЬ|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10,0|0,0|10,0|0,0|0,0|0,0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
Основной тип|FieldType.STRING|МОШЕННИЧЕСКИЕ ПРАКТИКЕ|КРАЖА|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
Описание|FieldType.STRING|ФИКТИВНЫЙ ПРОВЕРКИ|ЗА 500 ДОЛЛ. США|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
Описание расположения|FieldType.STRING||SCHOOL, ОБЩЕДОСТУПНЫЙ, СОЗДАНИЕ|10,0|0,0|10,0|0,0|0,0|1.0||||||||||||||
Фикс.|FieldType.BOOLEAN|False|False|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
Дом|FieldType.BOOLEAN|False|False|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
Пульс|FieldType.INTEGER|531|2433|10,0|0,0|10,0|0,0|0,0|0,0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
Округ|FieldType.INTEGER|5|24|10,0|0,0|10,0|0,0|0,0|0,0|5|5|5|6|13|19|24|24|24|13,5|6.94822|48.2778|0.0930109|-1.62325
Административный район|FieldType.INTEGER|1|48|10,0|0,0|10,0|0,0|0,0|0,0|1|5|1|9|22,5|40|48|48|48|24,5|16.2635|264.5|0.173723|-1.51271
Жилой микрорайон|FieldType.INTEGER|4\.|77|10,0|0,0|10,0|0,0|0,0|0,0|4\.|8.5|4\.|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379
Код ФБР|FieldType.INTEGER|6|11|10,0|0,0|10,0|0,0|0,0|0,0|6|6|6|6|11|11|11|11|11|9.4|2.36643|5.6|-0.702685|-1.59582
Координата X|FieldType.INTEGER|1.16309e+06|1.18336e+06|10,0|7.0|3.0|0.7|0,0|0,0|1.16309e+06|1.16309e+06|1.16309e+06|1.16401e+06|1.16678e+06|1.17921e+06|1.18336e+06|1.18336e+06|1.18336e+06|1.17108e+06|10793.5|1.165e+08|0.335126|-2.33333
Координата Y|FieldType.INTEGER|1.8315e+06|1.908e+06|10,0|7.0|3.0|0.7|0,0|0,0|1.8315e+06|1.8315e+06|1.8315e+06|1.83614e+06|1.85005e+06|1.89352e+06|1.908e+06|1.908e+06|1.908e+06|1.86319e+06|39905.2|1.59243e+09|0.293465|-2.33333
Год|FieldType.INTEGER|2016|2016|10,0|0,0|10,0|0,0|0,0|0,0|2016|2016|2016|2016|2016|2016|2016|2016|2016|2016|0|0|NaN|NaN
Обновлено|FieldType.DATE|2016-05-11 15:48:00+00:00|2016-05-27 15:45:00+00:00|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
Широта|FieldType.DECIMAL|41.6928|41.9032|10,0|7.0|3.0|0.7|0,0|0,0|41.6928|41.6928|41.6928|41.7057|41.7441|41.8634|41.9032|41.9032|41.9032|41.78|0.109695|0.012033|0.292478|-2.33333
Долгота|FieldType.DECIMAL|-87.6764|-87.6043|10,0|7.0|3.0|0.7|0,0|0,0|-87.6764|-87.6764|-87.6764|-87.6734|-87.6645|-87.6194|-87.6043|-87.6043|-87.6043|-87.6484|0.0386264|0.001492|0.344429|-2.33333
Расположение|FieldType.STRING||(41.903206037, -87.676361925)|10,0|0,0|10,0|0,0|0,0|7.0||||||||||||||

## <a name="impute-missing-values"></a>Добавление отсутствующих значений

В наборах данных значения null, значение NaN и значений, содержащих содержимое не считаются отсутствующих значений. Эти может повлиять на производительность моделей машинного обучения или привести к недопустимым выводы. Подстановки — распространенный подход для устранения недостающих значений, а также полезно, если у вас есть высокий процент отсутствующих значение записи, которые вы не можете просто удалить.

Из набора данных профиля, созданный в предыдущем разделе, мы видим, что `Latitude` и `Longitude` столбцы имеют высокий процент отсутствующих значений. В этом примере мы вычислить среднее значение и добавить отсутствующие значения для этих двух столбцов.

Во-первых, загрузите последние определения набора данных с помощью [ `get_definition()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-definition-version-id-none-) и сократить данные с помощью [ `keep_columns()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#keep-columns-columns--multicolumnselection--validate-column-exists--bool---false-----azureml-dataprep-api-dataflow-dataflow), поэтому мы просматривать только столбцы, мы хотим адрес.

```Python
from azureml.core.dataset import Dataset
import azureml.dataprep as dprep

# get the latest definition of Dataset
ds_def = dataset.get_definition()

# keep useful columns for this example
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
ds_def.head(3)
```

||ИД|Фикс.| Широта|Долгота|
-|---------|-----|---------|----------|
|0|10498554|False|41.692834|-87.604319|
|1|10516598|False| 41.744107 |-87.664494|
|2|10519196|False| NaN|NaN|

Затем проверьте `MEAN` значение широты столбца с помощью [ `summarize()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow-summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) функции. Эта функция принимает массив столбцов в параметре `group_by_columns`, чтобы указать совокупный уровень. `summary_columns` Принимает параметр `SummaryColumnsValue` функцию, которая указывает текущее имя столбца, имя нового вычисляемого поля, и `SummaryFunction` для выполнения.

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
|0|False|41.780049|

После проверки значений для добавления, использовать [ `ImputeMissingValuesBuilder` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) дополнительные предопределенной выражение, которое imputes либо вычисляемые столбцы `MIN`, `MAX`, `MEAN` значение, или `CUSTOM` значение. Если указано `group_by_columns`, то отсутствующие значения будут добавляться группой вместе с вычисленными `MIN`, `MAX` и `MEAN` для каждой группы.

[ `ImputeColumnArguments` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputecolumnarguments?view=azure-dataprep-pyfunction) Принимает строку, а column_id и `ReplaceValueFunction` для указания типа impute. На отсутствующее значение долготы добавить его с-87 на основе внешнего набора знаний.

```Python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id = 'Latitude',
                                          impute_function = dprep.ReplaceValueFunction.MEAN)

# impute with custom value -87
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=-87)
```

Добавить действия можно связывать друг с другом в `ImputeMissingValuesBuilder` с помощью [ `Builders` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py) класса функция [ `impute_missing_values()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py#impute-missing-values-impute-columns--typing-list-azureml-dataprep-api-builders-imputecolumnarguments----none--group-by-columns--typing-union-typing-list-str---nonetype----none-----azureml-dataprep-api-builders-imputemissingvaluesbuilder). Параметр `impute_columns` принимает массив объектов `ImputeColumnArguments`.

```Python
# get instance of ImputeMissingValuesBuilder
impute_builder = ds_def.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
```

Вызовите [ `learn()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#learn------none) функции для хранения impute действия и применять их в объект потока данных с помощью [ `to_dataflow()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#to-dataflow------azureml-dataprep-api-dataflow-dataflow).

```Python
impute_builder.learn()
ds_def = impute_builder.to_dataflow()
ds_def.head(3)
```

Как показано в следующей таблице выходные данные, отсутствующие широты было считать с `MEAN` значение `Arrest==False` с-87 было считать группу и отсутствующие долготу.

||ИД|Фикс.|Широта|Долгота
-|---------|-----|---------|----------
0|10498554|False|41.692834|-87.604319
1|10516598|False|41.744107|-87.664494
2|10519196|False|41.780049|-87.000000

Обновить определение набора данных, [ `update_definition()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#update-definition-definition--definition-update-message-) для сохранения действия выполняется преобразование.

```Python
dataset = dataset.update_definition(ds_def, 'Impute Missing')
dataset.head(3)
```

||ИД|Фикс.|Широта|Долгота
-|---------|-----|---------|----------
0|10498554|False|41.692834|-87.604319
1|10516598|False|41.744107|-87.664494
2|10519196|False|41.780049|-87.000000

## <a name="create-assertion-rules"></a>Создание правил утверждения

Часто данные мы работаем с при очистке и Подготовка данных является только подмножество общий объем данных, необходимые для рабочей среды. Таким образом некоторые из предположения, которые принимаются как часть наших очистки может оказаться, чтобы иметь значение false. Например в наборе данных, который постоянно обновляется, столбец, который изначально содержал только цифры в определенный диапазон может содержать более широкий диапазон значений при последующем выполнении. Эти ошибки часто приводят неработающих конвейеров или поврежденные данные.

Поддержка наборов данных, создании утверждений на данные, которые вычисляются при выполнении конвейера. Эти утверждения сообщить нам убедитесь, что наши предположения о данных по-прежнему быть точным и, если нет, для обработки ошибки соответствующим образом.

Например, если требуется ограничить `Latitude` и `Longitude` значения в наборе данных для определенных числовых диапазонов, [ `assert_value()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#assert-value-columns--multicolumnselection--expression--azureml-dataprep-api-expressions-expression--policy--azureml-dataprep-api-engineapi-typedefinitions-assertpolicy----assertpolicy-errorvalue--1---error-code--str----assertionfailed------azureml-dataprep-api-dataflow-dataflow) метод гарантирует, это всегда так.

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

||type|Min|Max|Количество|Отсутствующее количество|Присутствующее количество|Процент отсутствующих|Количество ошибок|Количество пустых|0,1 % квантилей|1 % квантилей|5 % квантилей|25 % квантилей|50 % квантилей|75 % квантилей|95 % квантилей|99 % квантилей|99,9 % квантилей|Среднее значение|стандартное отклонение.|Variance|Асимметрия|Эксцесс
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ИД|FieldType.INTEGER|1.04986e+07|1.05351e+07|10,0|0,0|10,0|0,0|0,0|0,0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e+07|1.05259e+07|1.05351e+07|1.05351e+07|1.05351e+07|1.05195e+07|12302.7|1.51358e+08|-0.495701|-1.02814
Фикс.|FieldType.BOOLEAN|False|False|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
Широта|FieldType.DECIMAL|41.6928|41.9032|10,0|0,0|10,0|0,0|0,0|0,0|41.6928|41.7185|41.6928|41.78|41.78|41.78|41.9032|41.9032|41.9032|41.78|0.0517107|0.002674|0.837593|1.05
Долгота|FieldType.INTEGER|-87|-87|10,0|0,0|10,0|0,0|3.0|0,0|-87|-87|-87|-87|-87|-87|-87|-87|-87|-87|0|0|NaN|NaN

Из профиля, вы увидите, что `Error Count` для `Longitude` столбца равно 3. Следующий код фильтрует набор данных, Получает ошибку и видит, какое значение вызывает утверждение, переход на другой. Далее измените код и очистка данных соответствующим образом.

```Python
from azureml.dataprep import col

ds_error = ds_def.filter(col('Longitude').is_error())
error = ds_error.head(10)['Longitude'][0]

print(error.originalValue)
```

    -87.60431945

## <a name="derive-columns-by-example"></a>Получение столбцов по образцу

Одним из более сложных средств для наборов данных является возможность извлечения столбцов, используя примеры из нужных результатов. Это позволяет выбрать пример, пакет SDK, поэтому он может создавать код для достижения предполагаемого преобразований.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')
dataset.head(3)
```

||ИД|Серийный номер|Дата|Блок|...|
-|---------|-----|---------|----|---
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|...
1|10516598|HZ258664|2016-04-15 17:00:00|082XX С MARSHFIELD ОХРАНИТЬ|...
2|10519196|HZ261252|2016-04-15 10:00:00|САКРАМЕНТО С 104XX ОХРАНИТЬ|...

Предположим, вам нужно преобразовать формат даты и времени "2016-04-04 22: 00 - 12 AM". В [ `derive_column_by_example()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#derive-column-by-example-source-columns--sourcecolumns--new-column-name--str--example-data--exampledata-----azureml-dataprep-api-dataflow-dataflow) аргумент, приведены примеры того, в нужный выходные данные в `example_data` параметр в следующем формате: *(первоначальные выходные данные, нужные выходные)* .

Ниже приведены два примера требуемого результата ("2016-04-04 23:56:00», «2016-04-04 10 PM-12 AM») и (" 2016-04-15 17:00:00», «2016-04-15 16: 00 - 18: 00»)

```Python
ds_def = dataset.get_definition()
ds_def = ds_def.derive_column_by_example(
        source_columns = "Date",
        new_column_name = "Date_Time_Range",
        example_data = [("2016-04-04 23:56:00", "2016-04-04 10PM-12AM"), ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")]
    )
ds_def.keep_columns(['ID','Date','Date_Time_Range']).head(3)
```

В следующей таблице Обратите внимание, что новый столбец, Date_Time_Range записей в указанном формате.

||ИД|Дата|Date_Time_Range
-|--------|-----|----
0|10498554|2016-04-04 23:56:00|2016-04-04 10 PM-12 AM
1|10516598|2016-04-15 17:00:00|2016-04-15 16: 00 - 18: 00
2|10519196|2016-04-15 10:00:00|2016-04-15 10: - 12: 00

```Python
# update Dataset definition to keep the transformation steps performed.
dataset = dataset.update_definition(ds_def, 'Derive Date_Time_Range')
```

## <a name="fuzzy-groupings"></a>Нечеткое группирование

Собирать данные из различных источников возможны вариации в написание, регистр букв или сокращения от те же сущности. Автоматически стандартизировать и согласовать эти варианты с функциональными возможностями Нечеткое группирование и кластеризация текста, в пакете SDK.

Например, столбец `inspections.business.city` содержит несколько видов Город имя «Сан-Франциско».

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local json file
dataset = Dataset.auto_read_files('./data/city.json')
dataset.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.Business.Address|inspections.Business.City|...|
-|-----|-------------------------|------------|--|---
0|16162|Индийская Foods экспресс N-Ezee|24-й St 3861|SF|...
1|67565|Король тайский лапшу кафе|1541 TARAVAL St|SAN FRANCISCO|...
2|67565|Король тайский лапшу кафе|1541 TARAVAL St|SAN FRANCISCO|...
3|68701|Grindz|832 clement St|SF|...
4\.|69186|Premier Catering & Events, Inc.|St 1255 22|S.F.|...

Мы используем [ `fuzzy_group_column()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#fuzzy-group-column-source-column--str--new-column-name--str--similarity-threshold--float---0-8--similarity-score-column-name--str---none-----azureml-dataprep-api-dataflow-dataflow) метод, чтобы добавить столбец с автоматически обнаруженными каноническая форма «Нижний Новгород». Аргументы `source_column` и `new_column_name` являются обязательными. У вас также есть возможность:

* Создание нового столбца, `similarity_score_column_name`, в котором показано показатель подобия между каждой парой исходные и канонические значения.

* Укажите `similarity_threshold`, который является минимальную оценку подобия для значений, которые необходимо сгруппировать.

```Python
# get the latest Dataset definition
ds_def = dataset.get_definition()
ds_def = ds_def.fuzzy_group_column(source_column='inspections.business.city',
                                       new_column_name='city_grouped',
                                       similarity_threshold=0.8,
                                       similarity_score_column_name='similarity_score')
ds_def.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.Business.Address|inspections.Business.City|city_grouped|similarity_score|...|
-|-----|-------------------------|------------|--|---|---|---
0|16162|Индийская Foods экспресс N-Ezee|24-й St 3861|SF|Сан-Франциско|0.814806|...
1|67565|Король тайский лапшу кафе|1541 TARAVAL St|SAN FRANCISCO|Сан-Франциско|1,000000|...
2|67565|Король тайский лапшу кафе|1541 TARAVAL St|SAN FRANCISCO|Сан-Франциско|1,000000|...
3|68701|Grindz|832 clement St|SF|Сан-Франциско|0.814806|...
4\.|69186|Premier Catering & Events, Inc.|St 1255 22|S.F.|Сан-Франциско|0.814806|...

В определении результирующего набора данных различных вариантов представления «Нижний Новгород» в данные были нормализованы ту же строку «Сан-Франциско».

Сохранить этот шаг "Нечеткое группирование" в последнее определение набора данных с помощью `update_definition()`.

```Python
dataset = dataset.update_definition(ds_def, 'fuzzy grouping')
```

## <a name="next-steps"></a>Дальнейшие действия

* [Управление жизненным циклом определения наборов данных](how-to-manage-dataset-definitions.md).

* Автоматические машинного обучения см. в разделе [руководстве](tutorial-auto-train-models.md) пример модели регрессии.

* Пакет SDK см. в разделе [Обзор](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) конструктивные шаблоны и примеры использования.

* Пример использования наборов данных, см. в разделе [примеры записных книжек](https://aka.ms/dataset-tutorial).
