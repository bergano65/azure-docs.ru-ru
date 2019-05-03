---
title: Создайте и зарегистрируйте наборы данных рабочей области
titleSuffix: Azure Machine Learning service
description: Узнайте, как создавать наборы данных из различных источников и зарегистрировать наборов данных в рабочей области
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/19
ms.openlocfilehash: 4b3fa69156146037ff59a41eab8c8373f6e01dc4
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029120"
---
# <a name="create-and-register-azure-machine-learning-datasets-preview"></a>Создание и регистрация наборы данных обучения машины Azure (Предварительная версия)

В этой статье вы узнаете, рабочие процессы машинного обучения Azure, чтобы создать и зарегистрировать наборов данных и способ доступа к ним для повторного использования в локальных и удаленных экспериментов.

Наборы данных (Предварительная версия) обучения Azure машины облегчают доступ и работы с данными. Наборы данных управлять данными в различных сценариях, таких как Обучение модели и создания конвейера. С помощью [пакета SDK для Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), можно работать с данными в популярных форматах, доступ к базовым хранилищем, просмотра и подготовить данные, управление жизненным циклом определения разных наборов данных и сравнения между наборами данных, используемых в обучение и в рабочей среде.

## <a name="prerequisites"></a>Технические условия

Чтобы создать и зарегистрировать наборы данных вам потребуется:

* Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).

* Рабочая область службы машинного обучения Azure. См. в разделе [создать рабочую область службы машинного обучения Azure](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* Машинного обучения Azure SDK для Python. Для установки или обновления до последней версии пакета SDK, см. в разделе [установить или обновить пакет SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

## <a name="create-datasets-from-local-files"></a>Создание наборов данных из локальных файлов

Загружать файлы с локального компьютера, указав путь к файлу или папке с [ `auto_read_files()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-) метода из `Dataset` класса.  Этот метод выполняет следующие действия не требуется указывать тип файла и анализ аргументов:

* Получение и задание разделитель.
* Пропускает пустые записи, в верхней части файла.
* Получение и задание строки заголовка.
* Получение и преобразование типов данных столбца.

```Python
from azureml.core import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

Кроме того можно используйте файл специальные функции для явного управления синтаксический анализ файла. В настоящее время пакет SDK наборы данных поддерживает [с разделителями](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-allfileshavesameheaders--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-norows--0---comment-none--include-path-false--archive-options-none-), [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-), [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-), [двоичных](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-), и [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-) форматы файлов.

## <a name="create-datasets-from-azure-datastores"></a>Создание наборов данных из хранилища данных Azure

Чтобы создать наборы данных из хранилища данных Azure, обязательно сделайте следующее:

* Убедитесь, что у вас есть доступ к зарегистрированным хранилище данных Azure владельца или участника.

* Импорт [ `Workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) и [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#definition) и `Dataset` пакеты из пакета SDK.

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
```

 `get()` Метод извлекает существующего хранилища данных в рабочей области.

```
dstore = Datastore.get(workspace, datastore_name)
```

Используйте `from_delimited_files()` метод для чтения в файлы с разделителями и создание наборов данных в памяти.

```Python
# create an in-memory Dataset on your local machine
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

||ИД|Серийный номер|Дата|Блок|IUCR|Основной тип|ОПИСАНИЕ|Описание расположения|Фикс.|Дом|...|Административный район|Жилой микрорайон|Код ФБР|Координата X|Координата Y|Год|Обновлено|Широта|Долгота|Расположение|
|--|--|---|---|---|---|----|------|-------|------|-----|---|----|----|-----|-----|------|----|-----|----|----|-----
|0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|МОШЕННИЧЕСКИЕ ПРАКТИКЕ|КРАЖИ ФИНАНСОВЫХ ДАННЫХ НА 300 ДОЛЛ. США|OTHER|FALSE|FALSE|...|9|50|11|1183356|1831503|2016|5/11/2016 15:48|41.69283384|-87.60431945|(41.692833841, -87.60431945)|
1|10516598|HZ258664|4/15/2016 17:00|082XX С MARSHFIELD ОХРАНИТЬ|890|КРАЖА| ПОСТРОЕНИЕ|ПРОЖИВАНИЯ|FALSE|FALSE|...|21|71|6|1166776|1850053|2016|5/12/2016 15:48|41.74410697|-87.66449429|(41.744106973, -87.664494285)
2|10519196|HZ261252|4/15/2016 10:00|САКРАМЕНТО С 104XX ОХРАНИТЬ|1154|МОШЕННИЧЕСКИЕ ПРАКТИКЕ|КРАЖИ ФИНАНСОВЫХ ДАННЫХ 300 ДОЛЛ. США И В РАЗДЕЛЕ|ПРОЖИВАНИЯ|FALSE|FALSE|...|19|74|11|||2016|5/12/2016 15:50
3|10519591|HZ261534|4/15/2016 9:00|113XX С НАПУГАННЫМ ОХРАНИТЬ|1120|МОШЕННИЧЕСКИЕ ПРАКТИКЕ|ПОДДЕЛКИ|ПРОЖИВАНИЯ|FALSE|FALSE|...|9|49|10|||2016|5/13/2016 15:51
4.|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|КРАЖА|ПОСТРОЕНИЕ|SCHOOL, ОБЩЕДОСТУПНЫЙ, СОЗДАНИЕ|FALSE|FALSE|...|40|13|6|||2016|5/25/2016 15:59|

## <a name="register-your-datasets-with-workspace"></a>Зарегистрировать в наборы данных в рабочей области

Используйте [ `register()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) метод для регистрации наборов данных в рабочую область для совместного использования и повторного использования в вашей организации, а также между различными экспериментов.

```Python
dataset = dataset.register(workspace = 'workspace_name',
                           name = "dataset_crime",
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> Параметр по умолчанию `register()` — "exist_ok = False". Если попытаться зарегистрировать набора данных с тем же именем без изменения этого параметра, выдается ошибка.

`register()` Метод обновляет определение набора данных, уже зарегистрированную с помощью настройки параметра `exist_ok = True`.

```Python
dataset = dataset.register(workspace = workspace_name,
                           name = "dataset_crime",
                           description = 'Training data',
                           exist_ok = True)
```

Используйте `list()` для просмотра всех зарегистрированных наборов данных в рабочей области.

```Python
Dataset.list(workspace_name)
```

Приведенный выше код приведет к следующему:

```Python
[Dataset(Name: dataset_crime,
         Workspace: workspace_name)]
```

## <a name="access-datasets-in-workspace"></a>Доступ к наборам данных в рабочей области

Зарегистрированные наборы данных являются доступными и готовых к использованию локально, удаленно и на вычислительные кластеры, как вычисление машинного обучения Azure. Чтобы повторно использовать зарегистрированный набор данных для экспериментов и вычислительные среды, используйте следующий код для получения рабочей области и зарегистрированный набор данных по имени.

```Python
workspace = Workspace.from_config()

dataset = workspace.Datasets['dataset_crime']
```

## <a name="next-steps"></a>Дальнейшие действия

* [Исследования и подготовки наборов данных](how-to-explore-prepare-data.md).
* [Управление жизненным циклом определения наборов данных](how-to-manage-dataset-definitions.md).
* Пример использования наборов данных, см. в разделе [примеры записных книжек](https://aka.ms/dataset-tutorial).