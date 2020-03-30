---
title: Версия набора данных
titleSuffix: Azure Machine Learning
description: Узнайте, как лучше настроить наборы данных и как версия работает с конвейерами машинного обучения.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.custom: ''
ms.openlocfilehash: acbd2e3ba756255cbc69ae8a7b7ad62d7a1c1c5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528478"
---
# <a name="version-and-track-datasets-in-experiments"></a>Версия и отслеживание наборов данных в экспериментах
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье вы узнаете, как можно отсутствовать и отслеживать наборы данных машинного обучения Azure для воспроизводимости. Версия набора данных — это способ закладки состояния ваших данных, чтобы можно было применить определенную версию набора данных для будущих экспериментов.

Типичные сценарии версий:

* Когда новые данные доступны для переподготовки
* При применении различных подходов к подготовке данных или инженерных характеристиках

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством необходимы указанные ниже компоненты.

- [Установлен SDK для машинного обучения Azure для Python.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) Этот SDK включает пакет [наборов лазурных данных.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py)
    
- [Рабочее пространство машинного обучения Azure](concept-workspace.md). Изыскните существующий, запустив следующий код, или [создайте новое рабочее пространство.](how-to-manage-workspace.md)

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- [Набор данных машинного обучения Azure.](how-to-create-register-datasets.md)

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Регистрация и извлечение версий набора данных

Зарегистрировав набор данных, можно использовать, повторно использовать и делиться им в экспериментах и с коллегами. Вы можете зарегистрировать несколько наборов данных под тем же именем и получить конкретную версию по имени и номеру версии.

### <a name="register-a-dataset-version"></a>Регистрация версии набора данных

Следующий код регистрирует новую `titanic_ds` версию набора `create_new_version` данных, установив параметр. `True` Если в рабочей `titanic_ds` области нет существующего набора данных, код создает `titanic_ds` новый набор данных с именем и устанавливает свою версию до 1.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```
Вы также можете зарегистрировать новую версию набора данных по адресу: 

### <a name="retrieve-a-dataset-by-name"></a>Извлечение набора данных по имени

По умолчанию метод [get_by_name()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) `Dataset` в классе возвращает последнюю версию набора данных, зарегистрированную в рабочем пространстве. 

Следующий код получает версию `titanic_ds` 1 набора данных.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>Версия наилучшей практики

При создании версии набора данных вы *не* создаете дополнительную копию данных в рабочем пространстве. Поскольку наборы данных являются ссылками на данные в службе хранения данных, у вас есть единый источник правды, управляемый службой хранения данных.

>[!IMPORTANT]
> Если данные, на которые ссылается ваш набор данных, перезаписаны или удалены, вызов определенной версии набора данных *не* возвращает изменение.

При загрузке данных из набора данных всегда загружается текущее содержимое данных, на который ссылается набор данных. Если вы хотите убедиться, что каждая версия набора данных воспроизводима, мы рекомендуем не изменять содержимое данных, на которое ссылается версия набора данных. При попадении новых данных сохраняйте новые файлы данных в отдельную папку данных, а затем создайте новую версию набора данных, включающий данные из этой новой папки.

Следующее изображение и пример кода показывают рекомендуемый способ структурирования папок данных и создания версий набора данных, которые ссылаются на эти папки:

![Структура папок](./media/how-to-version-track-datasets/folder-image.png)

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

## <a name="version-a-pipeline-output-dataset"></a>Версия набора выходных данных конвейера

Вкачестве ввода и вывода каждого шага конвейера машинного обучения можно использовать набор данных. При повторном запуске конвейеров выход каждого шага конвейера регистрируется как новая версия набора данных.

Поскольку конвейеры машинного обучения заполняют выход каждого шага в новую папку каждый раз при повторе нисполнии конвейера, наборы данных вывода версий воспроизводимы. Подробнее о [наборах данных в конвейерах.](how-to-create-your-first-pipeline.md#steps)

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData
from azureml.core. runconfig import CondaDependencies, RunConfiguration

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

conda = CondaDependencies.create(
    pip_packages=['azureml-defaults', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)

run_config = RunConfiguration()
run_config.environment.docker.enabled = True
run_config.environment.python.conda_dependencies = conda

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             runconfig=run_config,
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datasets-in-experiments"></a>Отслеживание наборов данных в экспериментах

Для каждого эксперимента по машинном обучению можно легко отслеживать наборы данных, используемые в качестве ввода через объект эксперимента. `Run`

Следующий код [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) использует метод для отслеживания того, какие наборы входных данных были использованы при запуске эксперимента:

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

Вы также можете `input_datasets` найти из https://ml.azure.com/экспериментов с помощью . 

Следующее изображение показывает, где найти набор входных данных эксперимента в студии Машинного обучения Azure. В этом примере перейдите на панель **экспериментов** и откройте вкладку **Properties** для определенного выполнения эксперимента. `keras-mnist`

![Наборы входных данных](./media/how-to-version-track-datasets/input-datasets.png)

Используйте следующий код для регистрации моделей с наборами данных:

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

После регистрации можно увидеть список моделей, зарегистрированных в https://ml.azure.com/наборе данных, с помощью Python или перейти к .

Следующее представление из панели **наборов данных** под **активами**. Выберите набор данных, а затем выберите вкладку **Модели** для списка моделей, зарегистрированных в наборе данных. 

![Модели наборов входных данных](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Обучение с наборами данных](how-to-train-with-datasets.md)
* [Дополнительные выборочные блокноты набора данных](https://aka.ms/dataset-tutorial)
