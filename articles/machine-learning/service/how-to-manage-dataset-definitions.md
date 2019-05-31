---
title: Версия определения набора данных с наборами данных машинного обучения Azure и управлять ими
titleSuffix: Azure Machine Learning service
description: Узнайте, как обновлять определения наборов данных и управление жизненным циклом определений
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 4476bdd902f054683fa544dc44b548689f3a1881
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241885"
---
# <a name="version-and-manage-your-dataset-definitions"></a>Версия вашего определения набора данных и управлять ими

Узнайте, как обновление и управление ими для определения наборов данных с помощью Azure Machine Learning наборы данных (Предварительная версия).

Определения наборов данных представляют собой инструкции, используемые для подготовки наборов данных, который включать оба ссылку на источник данных и шаги преобразования, выполняемые. Один набор данных может иметь много определений, и каждое определение имеет свой собственный жизненный цикл.

Следующий сценарий является примером использования определения наборов данных:

1. Вы создадите машинного обучения конвейера, который использует текущее определение набора данных.
1. Изменении базовых данных; были добавлены новые атрибуты.
1. Создается новая версия определении, добавляет дополнительные преобразования для обработки новых атрибутов.

В этом примере существующий конвейер продолжает потреблять исходная версия определения. Новой версии определение может использоваться для новых сценариев, таких как Обучение модели или создании конвейера.

## <a name="prerequisites"></a>Технические условия

Необходимо иметь подписку Azure и рабочую область, чтобы зарегистрировать ваш набор данных, чтобы управлять жизненным циклом определения наборов данных.

Пример файла, используемые в примерах в этом документе предоставляется [ https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv ](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv).

## <a name="create-and-update-definitions"></a>Создание и обновление определений

Сначала мы создадим и зарегистрировать набора данных в рабочей области.

```python
from azureml.core import Workspace, Datastore, Dataset

# change the configuration for workspace and Datastore
subscription_id = 'your subscription id'
resource_group = 'your resource group name'
workspace_name = 'your workspace name'
datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace(subscription_id, resource_group, workspace_name)

# get a Datastore that has already been created in the workspace
dstore = Datastore.get(workspace, datastore_name)

# create an in-memory Dataset from Datastore
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# register the Dataset with the workspace. if a Dataset with the same name already exists, retrieve it by turning `exist_ok = True`
dataset_name = 'crime dataset'
dataset = dataset.register(workspace = workspace, 
                 name = dataset_name, 
                 description = 'crime dataset for demo', 
                 tags = {'year':'2019', 'month':'Apr'},
                 exist_ok = True)
```

Первое определение набора данных (`version_id` = 1) при создании набора данных. Затем каждый раз при обновлении в определении набора данных, новый version_id присваивается последнее определение.

```python
# get the Dataset from the workspace by name
dataset = workspace.datasets['dataset_name']

# get the latest Dataset definition
ds_def = dataset.get_definition()

# update the Dataset definition to select only the columns I'm interested in
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])

# with this update, the latest definition for dataset now has `version_id = 2`
dataset = dataset.update_definition(ds_def, 'select useful column')

dataset = workspace.datasets['dataset_name']
dataset.head(5)
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
      <th>ИД</th>
      <th>Фикс.</th>
      <th>Широта</th>
      <th>Долгота</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10498554</td>
      <td>FALSE</td>
      <td>41.69283384</td>
      <td>-87.60431945</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10516598</td>
      <td>FALSE</td>
      <td>41.74410697</td>
      <td>-87.66449429</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10519196</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>10519591</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4.</th>
      <td>10534446</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

Это обновление определений не приведет к удалению предыдущих определения наборов данных. Вы по-прежнему можно получить доступ к и использовать предыдущими определениями.

```python
# specify `version_id` to get a previous definition 
ds_def_old = dataset.get_definition(version_id = 1)
ds_def_old.head(5)
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
      <th>ИД</th>
      <th>Серийный номер</th>
      <th>Date</th>
      <th>Блок</th>
      <th>IUCR</th>
      <th>Основной тип</th>
      <th>Описание</th>
      <th>Описание расположения</th>
      <th>Фикс.</th>
      <th>Дом</th>
      <th>...</th>
      <th>Административный район</th>
      <th>Жилой микрорайон</th>
      <th>Код ФБР</th>
      <th>Координата X</th>
      <th>Координата Y</th>
      <th>Год</th>
      <th>Обновлено</th>
      <th>Широта</th>
      <th>Долгота</th>
      <th>Расположение</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10498554</td>
      <td>HZ239907</td>
      <td>4/4/2016 23:56</td>
      <td>007XX E 111TH ST</td>
      <td>1153</td>
      <td>МОШЕННИЧЕСКИЕ ПРАКТИКЕ</td>
      <td>КРАЖИ ФИНАНСОВЫХ ДАННЫХ НА 300 ДОЛЛ. США</td>
      <td>OTHER</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>9</td>
      <td>50</td>
      <td>11</td>
      <td>1183356</td>
      <td>1831503</td>
      <td>2016</td>
      <td>5/11/2016 15:48</td>
      <td>41.69283384</td>
      <td>-87.60431945</td>
      <td>(41.692833841, -87.60431945)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10516598</td>
      <td>HZ258664</td>
      <td>4/15/2016 17:00</td>
      <td>082XX С MARSHFIELD ОХРАНИТЬ</td>
      <td>890</td>
      <td>КРАЖА</td>
      <td>ПОСТРОЕНИЕ</td>
      <td>ПРОЖИВАНИЯ</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>21</td>
      <td>71</td>
      <td>6</td>
      <td>1166776</td>
      <td>1850053</td>
      <td>2016</td>
      <td>5/12/2016 15:48</td>
      <td>41.74410697</td>
      <td>-87.66449429</td>
      <td>(41.744106973, -87.664494285)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10519196</td>
      <td>HZ261252</td>
      <td>4/15/2016 10:00</td>
      <td>САКРАМЕНТО С 104XX ОХРАНИТЬ</td>
      <td>1154</td>
      <td>МОШЕННИЧЕСКИЕ ПРАКТИКЕ</td>
      <td>КРАЖИ ФИНАНСОВЫХ ДАННЫХ 300 ДОЛЛ. США И В РАЗДЕЛЕ</td>
      <td>ПРОЖИВАНИЯ</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>19</td>
      <td>74</td>
      <td>11</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/12/2016 15:50</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>10519591</td>
      <td>HZ261534</td>
      <td>4/15/2016 9:00</td>
      <td>113XX С НАПУГАННЫМ ОХРАНИТЬ</td>
      <td>1120</td>
      <td>МОШЕННИЧЕСКИЕ ПРАКТИКЕ</td>
      <td>ПОДДЕЛКИ</td>
      <td>ПРОЖИВАНИЯ</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>9</td>
      <td>49</td>
      <td>10</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/13/2016 15:51</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4.</th>
      <td>10534446</td>
      <td>HZ277630</td>
      <td>4/15/2016 10:00</td>
      <td>055XX N KEDZIE AVE</td>
      <td>890</td>
      <td>КРАЖА</td>
      <td>ПОСТРОЕНИЕ</td>
      <td>SCHOOL, ОБЩЕДОСТУПНЫЙ, СОЗДАНИЕ</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>40</td>
      <td>13</td>
      <td>6</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/25/2016 15:59</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

Как видно из результата, первая версия определения набора данных по-прежнему предотвращает все столбцы данных преступления, независимо от обновления более поздней версии. Например, при наличии модели машинного обучения в настоящее время первой версии набора данных для использования `Date` столбец из набора данных как одной из функций, вы не столкнетесь с любая ошибка с последние обновления определений, поддерживающий только `ID`, `Arrest`, `Latitude`, `Longitude` столбцов из данных преступления.

## <a name="how-to-access-dataset-definitions"></a>Как получить доступ к определения наборов данных

Чтобы получить список всех определений, используйте `get_definitions()`. Чтобы получить конкретную версию определения, используйте `get_definition()`. В следующем примере показано получение списка всех определений и извлечь версии 1:

```python
# list all definitions for the dataset
dataset.get_definitions()
# get version ID 1
dataset.get_definition(version_id=1)
```

Выходные данные `get_definitions()` аналогичен приведенному ниже:

```text
{'2': VersionID: 2, State: active, Created: 2019-04-19 16:43:52.439890+00:00, Modified: 2019-04-19 16:43:52.439890+00:00, Notes: select useful column,
 '1': VersionID: 1, State: active, Created: 2019-04-19 16:39:14.112046+00:00, Modified: 2019-04-19 16:44:12.912663+00:00, Notes: None}
```

После получения определения, можно использовать с помощью моделей машинного обучения или в конвейер машинного обучения.

## <a name="manage-lifecycle-of-dataset-definitions"></a>Управлять жизненным циклом определения наборов данных

Жизненный цикл каждого определения набора данных можно управлять независимо друг от друга. Существует три этапа жизненного цикла: активные, устаревших или архивации.

### <a name="active"></a>Активен

Когда создается новое определение набора данных, он остается активным по умолчанию. 

### <a name="deprecate"></a>Отказаться от

Определения наборов данных можно быть устаревшим, если больше не рекомендуется использовать, и замены доступен. При нерекомендуемые определения набора данных используется в конвейеры машинного обучения, возвращается предупреждение, но выполнение не будет заблокировано.

При объявлении нерекомендуемым определения набора данных, укажите идентификатор набора данных и идентификатор версии определения набора данных для определения замены. Эта информация хранится и используется в предупреждающее сообщение при попытке использовать устаревшие определения набора данных.

```python
# get the definition that you want to deprecate
ds_def = dataset.get_definition(version_id = 1)

# deprecate it by providing the information for the replacement definition, which is recommended to be used in machine learning scenarios
ds_def.deprecate(deprecate_by_dataset_id=dataset.id, deprecated_by_definition_version=2)
```

### <a name="archive"></a>Архив

Определения наборов данных можно будет заархивировать, когда определения не должны использоваться для какой-либо причине (например, базовые данные более не доступны). При определении архивного набора данных используется в конвейеры машинного обучения, будет блокировать выполнение с ошибкой.

```python
# archive the definition with `version_id = 1`
ds_def = dataset.get_definition(version_id = 1)
ds_def.archive()
```

### <a name="reactivate"></a>Повторно активировать

Можно легко повторно активировать любые устаревшие или архивные определения набора данных.

```python
# reactivate Dataset definition with `version_id =1` which was archived in the previous step
ds_def = dataset.get_definition(version_id = 1)
ds_def.reactivate()
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о работе с наборами данных, см. в разделе [Создание и регистрация Azure Machine Learning, наборы данных](how-to-create-register-datasets.md).

Пример использования наборов данных, см. в разделе [примеры записных книжек](https://aka.ms/dataset-tutorial).
