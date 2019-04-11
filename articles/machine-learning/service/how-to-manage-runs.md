---
title: Запуск, отслеживать и отменять учебных запусков в Python
titleSuffix: Azure Machine Learning service
description: Узнайте, как начать, задайте для тега состояние и организация эксперименты машинного обучения.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 04/05/2019
ms.openlocfilehash: 82df2258116ce55fa440b67ec0a66b106d0d72c7
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471500"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Запуск, отслеживать и отменять учебных запусков в Python

[Azure Machine Learning и пакет SDK для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) предоставляет различные методы для мониторинга, организации и управления запусках для обучения и службы "Экспериментирование".

В этом практическом руководстве показаны примеры следующие задачи:

* [Монитор производительности](#monitor)
* [Отмена или вызвать сбой запуски](#cancel)
* [Создание дочерних запуски](#children)
* [Теги и нахождение запусков](#tag)

## <a name="prerequisites"></a>Технические условия

Вам потребуется следующее:

* Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).

* Рабочая область службы машинного обучения Azure. См. в разделе [создать рабочую область службы машинного обучения Azure](setup-create-workspace.md).

* Azure Machine Learning SDK для Python, установленной (версию 1.0.21 или более поздней версии). Чтобы установить или обновить до последней версии пакета SDK, перейдите к [установке или обновлении пакета SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) страницы.

    Чтобы проверить установленную версию пакета SDK для Azure Machine Learning, используйте следующий код.

    ```Python
    print(azureml.core.VERSION)
    ```

<a name="monitor"></a>

## <a name="start-a-run-and-set-its-status"></a>Запустить выполнение и задать его состояние

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

Получить состояние выполнения с [ `get_status()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--).

```Python
print(notebook_run.get_status())
```

Чтобы получить дополнительные сведения об использовании выполнения [ `get_details()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--).

```Python
notebook_run.get_details()
```

При успешном завершении запуска используйте [ `complete()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) метод, чтобы пометить его как завершенный.

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

Также можно использовать язык Python `with...as` шаблон. В этом контексте выполнения автоматически пометит сам как завершенный при запуска за пределы области действия. Таким образом не нужно вручную помечаем выполнение как завершенные.

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

<a name="cancel"></a>

## <a name="cancel-or-fail-runs"></a>Отмена или вызвать сбой запуски

 Если вы заметили ошибку или запуска, кажется, что для завершения, используйте некоторое время [ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) метод, чтобы остановить выполнение программы до его завершения и пометить его как отмененные.

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

Если выполнение завершается, но содержит сообщение об ошибке, использовался скрипт, неправильное обучения, можно использовать [ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-) метод, чтобы пометить его как не удалось.

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

<a name="children"></a>

## <a name="create-child-runs"></a>Создание дочерних запуски

Создание дочерних запуски для группировки связанных запусков, как и для настройки итераций разных гиперпараметров.

Данный пример кода использует hello_with_children.py сценарий для создания пакета из пяти запусков дочерние в отправленной выполнения с помощью [ `child_run()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) метод.

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
> Дочерняя задача выполняется полный автоматически при перемещении за пределы области действия.

Можно также запустить дочерние выполняется по одному, но так как каждый создания приводит к вызову сети, это менее эффективно, чем отправка пакетного выполнения.

Используйте [ `get_children()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) выполняется метод для запроса дочерних определенного родительского элемента.

```Python
list(parent_run.get_children())
```

<a name="tag"></a>

## <a name="tag-and-find-runs"></a>Теги и нахождение запусков

В службе машинного обучения Azure можно использовать свойства и теги, позволяющая организовать и запрос к выполняет важную информацию.

### <a name="add-properties-and-tags"></a>Добавить свойства и теги

Используйте [ `add_properties()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) добавляемый запусках метаданным с возможностью поиска. Например следующий код добавляет свойство «Автор» к запуску.

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Свойства являются неизменяемыми, что удобно, как он сохранялся для удобной организации аудита. В следующем примере кода будет привести к ошибке, так как мы уже добавили «azureml-user», так как свойство «Автор» в приведенном выше коде.

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

Теги, но являются изменяемыми. Используйте [ `tag()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) для добавления сведений для поиска и значимым для потребителей вашего эксперимента.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Можно также добавить тег простую строку. Он отображается в словаре тег со значением `None`.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

### <a name="query-properties-and-tags"></a>Запрос свойств и тегов

Вы можете запрос выполняет в эксперимент, который соответствует определенных свойств и тегов.

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

## <a name="example-notebooks"></a>Примеры записных книжек

Основные понятия, описанные в этой статье, демонстрируют следующие записные книжки:

* Дополнительные сведения о API ведения журнала, см. в разделе [записной книжки ведения журнала API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb).

* Дополнительные сведения об управлении выполняется с помощью пакета SDK для Azure Machine Learning, см. в разделе [управление запуски notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs).

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать, как выполнять вход метрики для экспериментов, см. в разделе [журнала метрик во время учебные запуски](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiment) статьи.