---
title: Создание наборов данных для доступа к данным с помощью azureml-DataSets
titleSuffix: Azure Machine Learning service
description: Узнайте, как создавать наборы данных из различных источников и регистрировать наборы данных в рабочей области.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/21/2019
ms.openlocfilehash: c5b423fca3e0ec116fceefb6867189f4f8413b96
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856087"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Создание и доступ к наборам данных (Предварительная версия) в Машинное обучение Azure

В этой статье вы узнаете, как создавать Машинное обучение Azure наборы данных (Предварительная версия) и как получать доступ к данным из локальных и удаленных экспериментов.

С помощью Машинное обучение Azure наборов данных можно: 

* **Сохранение одной копии данных в хранилище** , на которое ссылаются наборы данных

* **Анализ данных** с помощью исследовательского анализа данных 

* **Простой доступ к данным во время обучения модели** без беспокойства строки подключения или пути к данным

* **Совместное использование данных & совместно** с другими пользователями

## <a name="prerequisites"></a>предварительные требования

Для создания наборов данных и работы с ними требуется:

* Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).

* [Рабочая область службы машинное обучение Azure](how-to-manage-workspace.md)

* Установленный пакет [SDK для машинное обучение Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), включающий пакет azureml-DataSets.

> [!Note]
> Некоторые классы набора данных (Предварительная версия) имеют зависимости от пакета [azureml-](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) DataMarket. Для пользователей Linux эти классы поддерживаются только в следующих дистрибутивах:  Red Hat Enterprise Linux, Ubuntu, Fedora и CentOS.

## <a name="data-formats"></a>Форматы данных

Можно создать Машинное обучение Azure набор данных из следующих форматов:
+ [с разделителями](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-no-rows--0---comment-none--include-path-false--archive-options-none--partition-format-none-)
+ [json](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false--partition-format-none-)
+ [Excel](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true--partition-format-none-)
+ [Parquet](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false--partition-format-none-)
+ [Кадр данных Pandas](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-pandas-dataframe-dataframe--path-none--in-memory-false-)
+ [SQL query](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)
+ [binary](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)

## <a name="create-datasets"></a>Создание наборов данных 

Создавая набор данных, вы создаете ссылку на расположение источника данных вместе с копией его метаданных. Данные остаются в существующем расположении, поэтому дополнительные затраты на хранение не взимается.

### <a name="create-from-local-files"></a>Создать из локальных файлов

Загрузите файлы с локального компьютера, указав путь к файлу или папке с [`auto_read_files()`](/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false--partition-format-none-) помощью метода `Dataset` из класса.  Этот метод выполняет следующие действия, не требуя указывать тип файла или аргументы синтаксического анализа:

* Выведение и установка разделителя.
* Пропуск пустых записей в верхней части файла.
* Выведение и задание строки заголовка.
* Выведение и преобразование типов данных столбцов.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

Кроме того, можно использовать функции для конкретного файла, чтобы явно управлять синтаксическим анализом файла. 


### <a name="create-from-azure-datastores"></a>Создание из хранилищ данных Azure

Чтобы создать наборы данных из [хранилища данных Azure](how-to-access-data.md), сделайте следующее:

* Убедитесь, что `contributor` вы `owner` или имеете доступ к зарегистрированному хранилищу данных Azure.

* Создание набора данных путем ссылки на путь в хранилище данных 

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
dstore = Datastore.get(workspace, datastore_name)
```

Используйте метод, чтобы считать файлы с разделителями из [ссылки на](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)объект и создать незарегистрированный набор данных. `from_delimited_files()`

```Python
# create an in-memory Dataset on your local machine
dataset = Dataset.from_delimited_files(dstore.path('data/src/crime.csv'))

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

## <a name="register-datasets"></a>Регистрация наборов данных

Чтобы завершить процесс создания, зарегистрируйте наборы данных в рабочей области:

[`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) Используйте метод для регистрации наборов данных в рабочей области, чтобы их можно было использовать совместно с другими пользователями в различных экспериментах.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> Если `exist_ok = False` значение равно (по умолчанию) и вы пытаетесь зарегистрировать набор данных с тем же именем, что и другой, возникает ошибка. Задайте для значение, чтобы перезаписать существующий. `True`

## <a name="access-data-in-datasets"></a>Доступ к данным в наборах данных

Зарегистрированные наборы данных доступны локально и удаленно в таких кластерах, как Машинное обучение Azure вычислений. Для доступа к зарегистрированному набору данных во всех экспериментах используйте следующий код, чтобы получить рабочую область и зарегистрированный набор данных по имени.

```Python
workspace = Workspace.from_config()

# See list of datasets registered in workspace.
print(Dataset.list(workspace))

# Get dataset by name
dataset = Dataset.get(workspace, 'dataset_crime')

# Load data into pandas DataFrame
dataset.to_pandas_dataframe()
```

## <a name="next-steps"></a>Следующие шаги

* [Просмотр и подготовка наборов данных](how-to-explore-prepare-data.md).
* Пример использования наборов данных см. в [примере записных книжек](https://aka.ms/dataset-tutorial).
