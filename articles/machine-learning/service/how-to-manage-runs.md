---
title: Запуск, отслеживать и отменять учебных запусков в Python
titleSuffix: Azure Machine Learning service
description: Узнайте, как начать, задайте для тега состояние и организация экспериментов машинного обучения.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 04/05/2019
ms.openlocfilehash: c0c1c1353b12944fa913dfb0789192917b99f234
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59543599"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Запуск, отслеживать и отменять учебных запусков в Python

[Azure Machine Learning и пакет SDK для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) предоставляет различные методы для мониторинга, организации и управления запусках для обучения и службы "Экспериментирование".

В этой статье показаны примеры следующие задачи:

* Монитор производительности.
* Отмена или не выполняется.
* Создание дочерних запусков.
* Пометить и нахождение запусков.

## <a name="prerequisites"></a>Технические условия

Вам понадобятся следующие элементы:

* Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).

* [Рабочей области машинного обучения Azure службы](setup-create-workspace.md).

* Azure Machine Learning SDK для Python (версию 1.0.21 или более поздней версии). Для установки или обновления до последней версии пакета SDK, см. в разделе [установить или обновить пакет SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

    Чтобы проверить установленную версию пакета SDK для Azure Machine Learning, используйте следующий код:

    ```Python
    print(azureml.core.VERSION)
    ```

## <a name="start-a-run-and-its-logging-process"></a>Запуск выполнения и его процесса ведения журнала

Настройка эксперимента, импортировав [рабочей области](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py), [поэкспериментировать](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py), [запуска](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py), и [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) классы из [azureml.core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py) пакета.

```Python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Запуск и его процесса ведения журнала с начала [ `start_logging()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) метод.

```Python
notebook_run = exp.start_logging()

notebook_run.log(name="message", value="Hello from run!")
```

## <a name="monitor-the-status-of-a-run"></a>Отслеживать состояние выполнения

Получить состояние выполнения с [ `get_status()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) метод.

```Python
print(notebook_run.get_status())
```

Чтобы получить дополнительные сведения о выполнении, используйте [ `get_details()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) метод.

```Python
notebook_run.get_details()
```

При успешном завершении запуска используйте [ `complete()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) метод, чтобы пометить его как завершенный.

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

Если вы используете Python `with...as` шаблон, запуска автоматически пометит само по окончании выполнения выходит из области действия. Не нужно вручную помечаем выполнение как завершенные.

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

## <a name="cancel-or-fail-runs"></a>Отмена или вызвать сбой запуски

 Если вы Обратите внимание, что ошибка или выполнение занимает слишком много времени, чтобы завершить работу, использовать [ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) метод, чтобы остановить выполнение программы до его завершения и пометить его как отмененные.

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

Если завершения запуска, но содержит ошибку (например, скрипт неверные обучения использовалась), можно использовать [ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-) метод, чтобы пометить его как не удалось.

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

## <a name="create-child-runs"></a>Создание дочерних запуски

Создание дочерних запуски для группировки связанных запусков, например, для различных итераций, настройки гиперпараметров.

Данный пример кода использует `hello_with_children.py` скрипт для создания пакета из пяти запусков дочерний в отправленных выполнении с помощью [ `child_run()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) метод:

```Python
!more hello_with_children.py
run_config = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_script_run = exp.submit(run_config)
local_script_run.wait_for_completion(show_output=True)
print(local_script_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> При перемещении за пределы области действия, дочерние выполняется автоматически будут помечены как завершенные.

Можно также запустить дочерние выполняется по одному, но так как каждый создания приводит к вызову сети, это менее эффективно, чем отправка пакетного выполнения.

Чтобы запросить выполнения дочерних определенного родительского элемента, используйте [ `get_children()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) метод.

```Python
list(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>Теги и нахождение запусков

В службе машинного обучения Azure можно использовать свойства и теги, позволяющая организовать и запрос к выполняет важную информацию.

### <a name="add-properties-and-tags"></a>Добавить свойства и теги

Чтобы добавить метаданным с возможностью поиска работы, используйте [ `add_properties()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) метод. Например, следующий код добавляет `"author"` свойство к запуску:

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Свойства являются неизменяемыми, поэтому они создают, чтобы он сохранялся для удобной организации аудита. В следующем примере кода приведет к ошибке, в примере, так как мы уже добавили `"azureml-user"` как `"author"` значение свойства в приведенном выше коде:

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

В отличие от свойства теги являются изменяемыми. Чтобы добавить сведения для поиска и значимым для потребителей эксперимента, используйте [ `tag()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) метод.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Можно также добавить теги простую строку. Когда эти теги отображаются в словаре тег, у них есть значение `None`.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

### <a name="query-properties-and-tags"></a>Запрос свойств и тегов

Вы можете запрос выполняет в эксперимент, чтобы получить список запусков, соответствующих определенных свойств и тегов.

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

## <a name="example-notebooks"></a>Примеры записных книжек

Следующих записных книжках демонстрации понятий, которые в этой статье:

* Дополнительные сведения о API регистрации, см. в разделе [записной книжки ведения журнала API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb).

* Дополнительные сведения об управлении выполняется с помощью пакета SDK для Azure Machine Learning, см. в разделе [управление запуски notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs).

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать, как выполнять вход метрики для экспериментов, см. в разделе [журнала метрик во время учебные запуски](how-to-track-experiments.md).