---
title: Сравнить & воспроизвести данные с помощью набора данных моментальных снимков
titleSuffix: Azure Machine Learning service
description: Узнайте, как сравнение данных за период времени и обеспечения повторяемости с помощью набора данных моментальных снимков
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.date: 05/23/2019
ms.openlocfilehash: 525660be0f38c9458590e52cfcd575acb4cf5444
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66162054"
---
# <a name="compare-data-and-ensure-reproducibility-with-snapshots-preview"></a>Сравнения данных и обеспечения повторяемости с моментальными снимками (Предварительная версия)

В этой статье вы узнаете, как создавать и управлять ими моментальные снимки вашей [наборы данных обучения машины Azure](how-to-create-register-datasets.md) (наборы данных), чтобы записать или сравнение данных за период времени. Наборы данных упрощают доступ к и работа с данными в облаке в различных сценариях.

**Набор данных моментальных снимков** хранения данных профиля (сводные статистические данные) во время его создания. Можно также сохранить копию данных в моментальном снимке для обеспечения повторяемости.

>[!Important]
> Моментальные снимки к затратам на хранение. Сохранение копии данных в моментальном снимке требуется еще больше места. Используйте [ `dataset.delete_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-) когда они больше не нужны.

## <a name="when-to-use-snapshots"></a>Когда следует использовать моментальные снимки

Существует три основных использует для моментальных снимков.

+ **Проверка модели**: Сравните профиль данных между различными моментальными снимками между учебных запусков или с рабочими данными.

+ **Модель обеспечения повторяемости**: Воспроизвести результаты путем вызова моментального снимка, который включает в себя данные во время обучения.

+ **Отслеживание данных со временем**: См. в разделе, как набор данных развивался с [сравнение профилей](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#compare-profiles-rhs-dataset-snapshot--include-columns-none--exclude-columns-none--histogram-compare-method--histogramcomparemethod-wasserstein--0--)
  
## <a name="prerequisites"></a>Технические условия

Чтобы создать набор данных моментальных снимков, вам потребуется зарегистрированных Azure Machine Learning набора данных. Если нет, см. в разделе [наборы данных, создание и регистрация](how-to-create-register-datasets.md).

## <a name="create-dataset-snapshots"></a>Создание набора данных моментальных снимков

Чтобы создать моментальный снимок набора данных, используйте [ `dataset.create_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?#create-snapshot-snapshot-name--compute-target-none--create-data-snapshot-false--target-datastore-none-) из пакета SDK для Azure Machine Learning пакета наборы данных машинного обучения Azure.

По умолчанию моментальный снимок хранится профиль (сводные статистические данные) данных с последней версией [определения набора данных](how-to-manage-dataset-definitions.md) применения. Определение набора данных содержит запись действий преобразования, определенные для данных. Это отличный способ сделать воспроизводимый Подготовка данных для вашей работы.

При необходимости можно также включить копию данных в моментальном снимке путем добавления `create_data_snapshot = True`.  Эти данные могут быть полезными для обеспечения повторяемости.

В этом примере используется [образец данных crime](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) и набор данных с именем `dataset_crime` создан, используя сведения из раздела [«Создание и регистрация наборы данных»](how-to-create-register-datasets.md).

```Python
from azureml.core.workspace import Workspace
from azureml.core.dataset import Dataset
from azureml.data.dataset_snapshot import DatasetSnapshot
import datetime

# get existing workspace
workspace = Workspace.from_config()

# get existing, named dataset:
dataset = workspace.datasets['dataset_crime']

# assign name to snapshot
snapshot_name = 'snapshot_' + datetime.datetime.today().strftime('%Y%m%d%H%M%S')

# create snapshot to store profile from a remote environment
# and include copy of data
snapshot = dataset.create_snapshot(snapshot_name = snapshot_name,
                                   compute_target = remote_compute_target,
                                   create_data_snapshot = True)
```

Так как моментальные снимки создаются асинхронно, используйте [ `wait_for_completion()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#wait-for-completion-show-output-true--status-update-frequency-10-) метод, чтобы отслеживать процесс.

```Python
# monitor process every 10 seconds
snapshot.wait_for_completion(show_output=True, status_update_frequency=10)

# return snapshot just created
dataset.get_snapshot(snapshot_name)
```

Выходные данные:

```
Action status: Running
Action status: Running
Action status: Completed


DatasetSnapshot(Name: snapshot_20190411165420,
Dataset Id: 66cd0830-2f72-41e5-b677-d2d953f54821,
Workspace: "your_workspace_name",
Dataset definition version: 1,
Snapshot created date: 2019-05-11 17:24:00+00:00)
```

Используйте [ `dataset.delete_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-) когда они больше не нужны.

## <a name="find-snapshots"></a>Поиск моментальных снимков

Получить существующий моментальный снимок с помощью [ `get_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-snapshot-snapshot-name-).

Чтобы получить список сохраненных моментальных снимков из определенного набора данных, используйте [ `get_all_snapshots()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-all-snapshots--).

```Python
# Get named snapshot for this dataset
dataset.get_snapshot(snapshot_name)

# Get all snapshots for this dataset
dataset.get_all_snapshots()
```

## <a name="get-data-and-profiles-from-snapshots"></a>Получение данных и профилей из моментальных снимков

Каждый моментальный снимок создает профиль для набора данных, который включает сводные статистические данные и предоставляет возможность для сохранения копии данных.

### <a name="get-the-data-profile"></a>Получение данных профиля

Используйте [ `get_profile()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) право доступа к профилю данных.  Для сравнения данных обучения и рабочей среде, например, можно использовать этот профиль.

```Python
snapshot.get_profile()
```

||type|Минимум|Максимальные|Количество|Отсутствующее количество|Присутствующее количество|Процент отсутствующих|Количество ошибок|Количество пустых|0,1 % квантилей|1 % квантилей|5 % квантилей|25 % квантилей|50 % квантилей|75 % квантилей|95 % квантилей|99 % квантилей|99,9 % квантилей|Среднее значение|Стандартное отклонение|Variance|Асимметрия|Эксцесс
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ИД|FieldType.INTEGER|1.04986e+07|1.05351e+07|10,0|0,0|10,0|0,0|0,0|0,0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e+07|1.05259e+07|1.05351e+07|1.05351e+07|1.05351e+07|1.05195e+07|12302.7|1.51358e+08|-0.495701|-1.02814
Серийный номер|FieldType.STRING|HZ239907|HZ278872|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
Date|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
Заблокировать|FieldType.STRING|004XX С KILBOURN ОХРАНИТЬ|113XX С НАПУГАННЫМ ОХРАНИТЬ|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10,0|0,0|10,0|0,0|0,0|0,0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
Основной тип|FieldType.STRING|МОШЕННИЧЕСКИЕ ПРАКТИКЕ|КРАЖА|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
Описание|FieldType.STRING|ФИКТИВНЫЙ ПРОВЕРКИ|ЗА 500 ДОЛЛ. США|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
Описание расположения|FieldType.STRING||SCHOOL, ОБЩЕДОСТУПНЫЙ, СОЗДАНИЕ|10,0|0,0|10,0|0,0|0,0|1.0||||||||||||||
Фикс.|FieldType.BOOLEAN|False|False|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
Дом|FieldType.BOOLEAN|False|False|10,0|0,0|10,0|0,0|0,0|0,0||||||||||||||
Пульс|FieldType.INTEGER|531|2433|10,0|0,0|10,0|0,0|0,0|0,0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
Округ|FieldType.INTEGER|5|24|10,0|0,0|10,0|0,0|0,0|0,0|5|5|5|6|13|19|24|24|24|13,5|6.94822|48.2778|0.0930109|-1.62325
Административный район|FieldType.INTEGER|1|48|10,0|0,0|10,0|0,0|0,0|0,0|1|5|1|9|22,5|40|48|48|48|24,5|16.2635|264.5|0.173723|-1.51271
Жилой микрорайон|FieldType.INTEGER|4.|77|10,0|0,0|10,0|0,0|0,0|0,0|4.|8.5|4.|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379

### <a name="get-the-data-from-the-snapshot"></a>Получение данных из моментального снимка

Чтобы получить копию данные, сохраненные в моментальном снимке набора данных, создать кадр данных pandas с [ `to_pandas_dataframe()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#to-pandas-dataframe--) метод.

Этот метод завершается ошибкой, если копия данных не было запрошено во время создания моментального снимка.

```Python
snapshot.to_pandas_dataframe().head(3)
```

||ИД|Серийный номер|Date|Заблокировать|IUCR|Основной тип|Описание|Описание расположения|Фикс.|Дом|...|Административный район|Жилой микрорайон|Код ФБР|Координата X|Координата Y|Год|Обновлено|Широта|Долгота|Расположение
-|--|-----------|----|-----|----|------------|-----------|--------------------|------|--------|---|----|--------------|--------|------------|------------|----|----------|--------|---------|--------
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|1153|МОШЕННИЧЕСКИЕ ПРАКТИКЕ|КРАЖИ ФИНАНСОВЫХ ДАННЫХ НА 300 ДОЛЛ. США|OTHER|False|False|...|9|50|11|1183356.0|1831503.0|2016|2016-05-11 15:48:00|41.692834|-87.604319|(41.692833841, -87.60431945)
1|10516598|HZ258664|2016-04-15 17:00:00|082XX С MARSHFIELD ОХРАНИТЬ|890|КРАЖА|ПОСТРОЕНИЕ|ПРОЖИВАНИЯ|False|False|...|21|71|6|1166776.0|1850053.0|2016|2016-05-12 15:48:00|41.744107|-87.664494|(41.744106973, -87.664494285)
2|10519196|HZ261252|2016-04-15 10:00:00|САКРАМЕНТО С 104XX ОХРАНИТЬ|1154|МОШЕННИЧЕСКИЕ ПРАКТИКЕ|КРАЖИ ФИНАНСОВЫХ ДАННЫХ 300 ДОЛЛ. США И В РАЗДЕЛЕ|ПРОЖИВАНИЯ|False|False|...|19|74|11|NaN|NaN|2016|2016-05-12 15:50:00|NaN|NaN|

## <a name="next-steps"></a>Дальнейшие действия

* Пакет SDK см. в разделе [Обзор](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) конструктивные шаблоны и примеры использования.

* Пример с использованием набора данных моментальных снимков, см. в разделе [примеры записных книжек](https://aka.ms/dataset-tutorial).
