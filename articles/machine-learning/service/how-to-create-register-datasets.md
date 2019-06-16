---
title: Создание наборов данных для доступа к данным с наборами данных машинного обучения Azure
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
ms.date: 05/21/2019
ms.openlocfilehash: b4c22caae86e20b8379db2b7feffb1ca82001239
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66753159"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Создание и доступ к наборам данных (Предварительная версия) в машинном обучении Azure

В этой статье вы узнаете, как создавать наборы данных машинного обучения Azure (Предварительная версия) и способах доступа к данным из локальных и удаленных экспериментов.

С помощью управляемого наборов данных вы можете: 
* **Легкого доступа к данным во время обучения модели** без повторного подключения к мест хранения

* **Обеспечить согласованность данных и обеспечения повторяемости** используют тот же указатель через экспериментов: записных книжек, автоматических машинного обучения, конвейеры, графический интерфейс

* **Совместное использование данных и сотрудничать** с другими пользователями

* **Просмотр данных** & управлять жизненным циклом моментальные снимки данных и версии

* **Сравнение данных** в обучении в рабочей среде


## <a name="prerequisites"></a>Технические условия

Для создания и работы с наборами данных, вам потребуется:

* Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).

* [Рабочей области службы машинного обучения Azure](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)

* [Azure Machine Learning и пакет SDK для Python, установленной](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), который включает пакет наборы данных машинного обучения Azure.

> [!Note]
> Некоторые классы наборов данных (Предварительная версия) имеют зависимости от [azureml dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) пакета (GA). Для пользователей Linux эти классы поддерживаются только для следующих дистрибутивов:  Red Hat Enterprise Linux, Ubuntu, Fedora и CentOS.

## <a name="data-formats"></a>Форматы данных

Можно создать набор данных Azure Machine Learning из следующие данные:
+ [С разделителями](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset#from-delimited-files-path--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-no-rows--0---comment-none--include-path-false--archive-options-none-)
+ [binary](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)
+ [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-)
+ [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-)
+ [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-)
+ [база данных SQL Azure;](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)
+ [Azure Data Lake Унив. 1](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)

## <a name="create-datasets"></a>Создание наборов данных 

Вы можете взаимодействовать с наборов данных с помощью пакета наборы данных машинного обучения Azure в [пакета SDK Azure Machine Learning Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) и специально [ `Dataset` класс](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py).

### <a name="create-from-local-files"></a>Создание из локальных файлов

Загружать файлы с локального компьютера, указав путь к файлу или папке с [ `auto_read_files()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-) метода из `Dataset` класса.  Этот метод выполняет следующие действия не требуется указывать тип файла и анализ аргументов:

* Получение и задание разделитель.
* Пропускает пустые записи, в верхней части файла.
* Получение и задание строки заголовка.
* Получение и преобразование типов данных столбца.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

Кроме того можно используйте файл специальные функции для явного управления синтаксический анализ файла. 


### <a name="create-from-azure-datastores"></a>Создание из хранилища данных Azure

Создание наборов данных из хранилища данных Azure:

* Убедитесь в наличии `contributor` или `owner` доступ к зарегистрированным хранилище данных Azure.

* Импорт [ `Workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) и [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#definition) и `Dataset` пакеты из пакета SDK.

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
```

 `get()` Метод извлекает существующего хранилища данных в рабочей области.

```
dstore = Datastore.get(workspace, datastore_name)
```

Используйте `from_delimited_files()` метод для чтения в файлы с разделителями и создайте набор данных отменена.

```Python
# create an in-memory Dataset on your local machine
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

## <a name="register-datasets"></a>Регистрация наборов данных

Чтобы завершить процесс создания, зарегистрируйте наборов данных с помощью рабочей области:

Используйте [ `register()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) метод, чтобы зарегистрировать наборов данных в рабочую область, чтобы они могли совместно с другими пользователями и повторно использовать в различных экспериментов.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> Если `exist_ok = False` (по умолчанию), и вы пытаетесь зарегистрировать набора данных с тем же именем, что и другой, возникает ошибка. Значение `True` перезаписать существующие.

## <a name="access-data-in-datasets"></a>Доступ к данным в наборах данных

Зарегистрированные наборы данных являются доступными и готовых к использованию локально, удаленно и на вычислительные кластеры, как вычисление машинного обучения Azure. Чтобы повторно использовать зарегистрированный набор данных для экспериментов и вычислительные среды, используйте следующий код для получения рабочей области и зарегистрированный набор данных по имени.

```Python
workspace = Workspace.from_config()

# See list of datasets registered in workspace.
Dataset.list(workspace)

# Get dataset by name
dataset = workspace.datasets['dataset_crime']
```

## <a name="next-steps"></a>Дальнейшие действия

* [Исследования и подготовки наборов данных](how-to-explore-prepare-data.md).
* [Управление жизненным циклом определения наборов данных](how-to-manage-dataset-definitions.md).
* Пример использования наборов данных, см. в разделе [примеры записных книжек](https://aka.ms/dataset-tutorial).
