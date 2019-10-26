---
title: Управление версиями набора данных
titleSuffix: Azure Machine Learning service
description: Узнайте, как использовать версии наборов данных и как работает управление версиями с помощью конвейеров машинного обучения.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: sihhu
ms.reviewer: nibaccam
ms.date: 10/25/2019
ms.custom: ''
ms.openlocfilehash: a361800623796cb3bc26a47c4f8f532503836124
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72902006"
---
# <a name="version-and-track-datasets-in-experiments"></a>Версии и отслеживание наборов данных в экспериментах

В этом пошаговом окне вы узнаете, как выполнять версию и отслеживание Машинное обучение Azure наборов данных для воспроизводимость. Управление версиями набора данных — это способ закладки состояния данных, поэтому можно применить определенную версию набора данных для будущих экспериментов.

Типичные сценарии, которые следует учитывать при управлении версиями:

* Когда новые данные доступны для повторного обучения.
* При применении различных подходов к подготовке данных или проектированию компонентов.

## <a name="prerequisites"></a>Технические условия

Для этого вам потребуются:

- [Установленный пакет SDK для машинное обучение Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), включающий пакет [azureml-DataSets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py) .
    
- [Рабочая область машинное обучение Azure](concept-workspace.md). Извлеките существующий код с помощью следующего кода или [Создайте новую рабочую область](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- [Набор данных машинное обучение Azure](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Регистрация и получение версий наборов данных

Регистрация набора данных позволяет использовать версию, многократное использование и совместно использовать ее в экспериментах и с коллегами. Можно зарегистрировать несколько наборов данных с одним и тем же именем и получить определенную версию по имени и номеру версии.

### <a name="register-a-dataset-version"></a>Регистрация версии набора данных

Следующий код регистрирует новую версию набора данных `titanic_ds`, установив для параметра `create_new_version` значение `True`. Если в рабочей области нет существующих `titanic_ds`, он создает новый набор данных с именем `titanic_ds` и устанавливает для его версии значение 1.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>Получение набора данных по имени

По умолчанию метод [get_by_name ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) в классе `Dataset` возвращает последнюю версию набора данных, зарегистрированную в рабочей области. 

Следующий код возвращает версию 1 набора данных `titanic_ds`.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>Рекомендации по управлении версиями

При создании версии набора данных вы **не** создаете дополнительные копии данных с рабочей областью. Наборы данных — это ссылки на данные в службе хранилища, поэтому у вас есть только один источник истинного управления службой хранилища. 

>[!IMPORTANT]
> Если данные, на которые ссылается набор данных, перезаписываются или удаляются, вызов определенной версии набора данных не может отменить изменения. 

При загрузке данных из набора данных всегда будет загружаться текущее содержимое данных, на которое ссылается набор данных. Если вы хотите обеспечить воспроизводимость каждой версии набора данных, мы рекомендуем не изменять содержимое данных, на которое ссылается версия набора данных. Когда поступают новые данные, сохраняйте новые файлы данных в отдельную папку данных и создайте новую версию набора данных для включения данных из этой новой папки данных.

На следующем рисунке и образце кода показан рекомендуемый способ структурирования папок данных и создания версий наборов данных, ссылающихся на эти папки.

![Структура папок](media/how-to-version-datasets/folder-image.png)

```Python
from azureml.core import Dataset

# get the default datastore of the workspace
datastore = workspace.get_default_datastore()

# create & register weather_ds version 1 pointing to all files in the folder of week 27
datastore_path1 = [(datastore, 'Weather/week 27')]
dataset1 = Dataset.File.from_files(path=datastore_path1)
dataset1.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27',
                  create_new_version = True)

# create & register weather_ds version 2 pointing to all files in the folder of week 27 and 28
datastore_path2 = [(datastore, 'Weather/week 27'), (datastore, 'Weather/week 28')]
dataset2 = Dataset.File.from_files(path = datastore_path2)
dataset2.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27, 28',
                  create_new_version = True)

```

<a name="pipeline"></a>

## <a name="version-a-pipeline-output-dataset"></a>Версия выходного набора данных конвейера

Вы можете использовать набор данных в качестве входных и выходных данных каждого этапа конвейера машинного обучения (ML). При повторном запуске конвейеров выходные данные каждого шага конвейера будут зарегистрированы как новая версия набора данных. 

Поскольку конвейеры машинного обучения заполняют выходные данные каждого шага в новую папку при каждом перезапуске конвейера, выходные наборы данных с управлением версиями воспроизводимы.

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datasets-in-experiments"></a>Отслеживание наборов данных в экспериментах

Для каждого эксперимента машинного обучения можно легко отслеживать наборы данных, используемые в качестве входных данных, с помощью объекта `Run` зарегистрированной модели.

Используйте следующий код для регистрации моделей с наборами данных.

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

После регистрации вы сможете просмотреть список моделей, зарегистрированных в наборе данных, с помощью Python или [целевой страницы рабочей области](https://ml.azure.com/).

В следующем коде используется метод [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) для наблюдения за тем, какие входные наборы данных использовались при выполнении этого эксперимента.

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
train_dataset = inputs[0]['dataset']

# list the files referenced by train_dataset
train_dataset.to_path()
```

Вы также можете найти `input_datasets` из экспериментов, используя [целевую страницу рабочей области (Предварительная версия)](https://ml.azure.com/). 

На следующем рисунке показано, где найти входной набор данных эксперимента на целевой странице рабочей области. В этом примере перейдите к области **эксперименты** и откройте вкладку **свойства** для конкретного запуска эксперимента, `keras-mnist`. 

![Входные наборы данных](media/how-to-version-datasets/input-datasets.png)

Вы также можете найти модели, которые использовали набор данных, с целевой страницей рабочей области. Следующее представление находится в колонке наборы данных в разделе активы. Выберите набор данных и перейдите на вкладку модели, чтобы получить список моделей, использующих этот набор данных. 

![Модели входных наборов данных](media/how-to-version-datasets/dataset-models.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Обучение с наборами данных](how-to-train-with-datasets.md).
* [Дополнительные образцы записных книжек для наборов данных](https://aka.ms/dataset-tutorial).
