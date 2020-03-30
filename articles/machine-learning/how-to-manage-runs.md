---
title: Запуск, мониторинг и отмена тренировочных забегов в Python
titleSuffix: Azure Machine Learning
description: Узнайте, как начать, установить статус, пометить и организовать свои эксперименты машинного обучения.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 01/09/2020
ms.openlocfilehash: 8c261a010a1e8f4d1be9b3883510eb38c37a15ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296877"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Запуск, мониторинг и отмена тренировочных забегов в Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

[В Azure Machine Learning SDK для Python,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) [Machine Learning CLI](reference-azure-machine-learning-cli.md)и [студии Машинного обучения Azure](https://ml.azure.com) предоставляются различные методы мониторинга, организации и управления пробегами для обучения и экспериментов.

В этой статье приводятся примеры следующих задач:

* Мониторинг производительности выполнения.
* Отмена или сбой выполняет.
* Создание детских бегов.
* Тег и найти работает.

## <a name="prerequisites"></a>Предварительные требования

Вам понадобятся следующие элементы:

* Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree) уже сегодня.

* [Рабочее пространство машинного обучения Azure](how-to-manage-workspace.md).

* SDK Для Python (версия 1.0.21 или более позднего времени) — SDK для машинного обучения Azure. Чтобы установить или обновить последнюю версию SDK, [см. Установить или обновить SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

    Чтобы проверить версию SDK Для машинного обучения Azure, используйте следующий код:

    ```python
    print(azureml.core.VERSION)
    ```

* Расширение [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) и [CLI для машинного обучения Azure.](reference-azure-machine-learning-cli.md)

## <a name="start-a-run-and-its-logging-process"></a>Запуск запуска и процесс регистрации

### <a name="using-the-sdk"></a>Использование пакета SDK

Навлажьте эксперимент, импортируя классы [Workspace,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) [Experiment,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)и [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) из пакета [azureml.core.](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py)

```python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Запустите запуск и процесс [`start_logging()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) регистрации с помощью метода.

```python
notebook_run = exp.start_logging()
notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>Использование интерфейса командной строки

Чтобы начать эксперимент, используйте следующие действия:

1. От запроса оболочки или команды используйте Azure CLI для проверки подлинности подписки Azure:

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

1. Прикрепите конфигурацию рабочего пространства к папке, содержащей сценарий обучения. Замените `myworkspace` рабочее пространство машинного обучения Azure. Заменить `myresourcegroup` группу ресурсов Azure, содержащую рабочее пространство:

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Эта команда `.azureml` создает субдиректорию, которая содержит примеры файлов среды runconfig и conda. Он также `config.json` содержит файл, который используется для связи с вашим рабочим пространством машинного обучения Azure.

    Для получения дополнительной информации, см [Az мл папку прикрепить](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

2. Чтобы запустить запуск, используйте следующую команду. При использовании этой команды укажите имя файла runconfig (текст до \*.runconfig, если вы смотрите на файловую систему) по параметру -c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > Команда `az ml folder attach` создала `.azureml` субдиректор, который содержит два примера файлов runconfig.
    >
    > Если у вас есть скрипт Python, который создает объект конфигурации запуска программно, вы можете использовать [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) для сохранения его в качестве файла runconfig.
    >
    > Для более примера runconfig файлов, см. [https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml)

    Для получения дополнительной [az ml run submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)информации см.

### <a name="using-azure-machine-learning-studio"></a>Использование студии машинного обучения Azure

Для запуска конвейера в проекте (предварительный просмотр) используйте следующие действия:

1. Установите цель вычислений по умолчанию для конвейера.

1. Выберите **Выполнить** в верхней части холста конвейера.

1. Выберите эксперимент для группы запусков конвейера.

## <a name="monitor-the-status-of-a-run"></a>Мониторинг состояния запуска

### <a name="using-the-sdk"></a>Использование пакета SDK

Получите статус запуска с [`get_status()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) помощью метода.

```python
print(notebook_run.get_status())
```

Чтобы получить идентификатор запуска, время выполнения и [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) дополнительные сведения о запуске, используйте метод.

```python
print(notebook_run.get_details())
```

Когда запуск завершится успешно, [`complete()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) используйте метод, чтобы отметить его как завершенный.

```python
notebook_run.complete()
print(notebook_run.get_status())
```

Если вы используете `with...as` шаблон проектирования Python, запуск автоматически пометит себя как завершенный, когда запуск выходит за рамки. Вам не нужно вручную отмечать пробег по мере завершения.

```python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print(notebook_run.get_status())

print(notebook_run.get_status())
```

### <a name="using-the-cli"></a>Использование интерфейса командной строки

1. Чтобы просмотреть список запусков для эксперимента, используйте следующую команду. Замените `experiment` название эксперимента:

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    Эта команда возвращает документ JSON, в который перечислена информация о запусках для этого эксперимента.

    Для получения дополнительной информации, см [Az мл список экспериментов](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

2. Для просмотра информации о конкретном запуске используйте следующую команду. Заменить `runid` идентификатор омичения пробега:

    ```azurecli-interactive
    az ml run show -r runid
    ```

    Эта команда возвращает документ JSON, в который перечислена информация о запуске.

    Для получения дополнительной информации, см [Az мл запустить шоу](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).


### <a name="using-azure-machine-learning-studio"></a>Использование студии машинного обучения Azure

Для просмотра количества активных запусков для эксперимента в студии.

1. Перейдите в раздел **Эксперименты.** 

1. Выберите эксперимент.

    На странице эксперимента можно увидеть количество активных вычислительных целей и продолжительность каждого запуска. 

1. Выберите конкретный номер выполнения.

1. Во вкладке **Журналы** можно найти журналы диагностики и ошибок для выполнения конвейера.


## <a name="cancel-or-fail-runs"></a>Отмена или сбой запусков

Если вы заметили ошибку или если ваш запуск занимает слишком много времени, чтобы закончить, вы можете отменить запуск.

### <a name="using-the-sdk"></a>Использование пакета SDK

Чтобы отменить запуск с помощью [`cancel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) SDK, используйте метод:

```python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_script_run = exp.submit(run_config)
print(local_script_run.get_status())

local_script_run.cancel()
print(local_script_run.get_status())
```

Если запуск завершается, но содержит ошибку (например, был использован неправильный [`fail()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)#fail-error-details-none--error-code-none---set-status-true-) сценарий обучения), можно использовать метод, чтобы отметить его как неудачный.

```python
local_script_run = exp.submit(run_config)
local_script_run.fail()
print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>Использование интерфейса командной строки

Чтобы отменить запуск с помощью CLI, используйте следующую команду. Заменить `runid` идентификатором пробега

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Для получения дополнительной [az ml run cancel](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel)информации см.

### <a name="using-azure-machine-learning-studio"></a>Использование студии машинного обучения Azure

Чтобы отменить запуск в студии, используя следующие действия:

1. Перейдите к бегущей конвейеру в разделе **Эксперименты** или **Трубопроводы.** 

1. Выберите номер выполнения конвейера, который вы хотите отменить.

1. В панели инструментов выберите **Отмена**


## <a name="create-child-runs"></a>Создание детских запусков

Создание запусков доемов для группирования связанных с ними запусков, например для различных итераций гиперпараметрной настройки.

> [!NOTE]
> Детские запуски могут быть созданы только с помощью SDK.

В этом примере кода `hello_with_children.py` используется скрипт для создания пакета из [`child_run()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) пяти запусков из числа представленных запусков с помощью метода:

```python
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
> По мере того, как они выходят за рамки, детские запуски автоматически помечаются как завершенные.

Чтобы создать много детей работает [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) эффективно, используйте метод. Поскольку каждое творение приводит к сетевому вызову, создание партии запусков является более эффективным, чем создание их по одному.

### <a name="submit-child-runs"></a>Отправка детских запусков

Запуски детей также могут быть отправлены с родительского запуска. Это позволяет создавать иерархии родительских и детских запусков, каждый из которых работает на различных вычислительных целях, соединенных общим идентификатором родительского запуска.

Используйте метод ["submit_child()",](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#submit-child-config--tags-none----kwargs-) чтобы отправить запуск ребенка из родительского запуска. Для этого в сценарии родительского запуска получите контекст выполнения ``submit_child`` и отправьте запуск ребенка методом экземпляра контекста.

```python
## In parent run script
parent_run = Run.get_context()
child_run_config = ScriptRunConfig(source_directory='.', script='child_script.py')
parent_run.submit_child(child_run_config)
```

В рамках выполнения ребенка можно просмотреть идентификатор родительского запуска:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Запуски запросов

Для запроса запуска ребенка определенного [`get_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) родителя воспользуйтесь методом. Аргумент ``recursive = True`` позволяет задать вопрос вложенных деревьев детей и внуков.

```python
print(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>Тег и поиск работает

В Azure Machine Learning вы можете использовать свойства и теги, чтобы помочь организовать и запросить запрос на получение важной информации.

### <a name="add-properties-and-tags"></a>Добавление свойств и тегов

#### <a name="using-the-sdk"></a>Использование пакета SDK

Чтобы добавить в свои пробежки [`add_properties()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) метаданные, пригодные для поиска, используйте метод. Например, следующий код `"author"` добавляет свойство в запуск:

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Свойства неизменяемы, поэтому они создают постоянную запись для целей аудита. Следующий пример кода приводит к ошибке, `"author"` потому что мы уже добавили `"azureml-user"` значение свойства в предыдущем коде:

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

В отличие от свойств, теги изменяются. Чтобы добавить поисковую и значимую [`tag()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) информацию для потребителей эксперимента, используйте метод.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Вы также можете добавить простые теги строки. Когда эти теги отображаются в словаре `None`тегов в качестве ключей, они имеют значение.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>Использование интерфейса командной строки

> [!NOTE]
> Используя CLI, можно только добавлять или обновлять теги.

Чтобы добавить или обновить тег, используйте следующую команду:

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

Для получения дополнительной [az ml run update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update)информации см.

### <a name="query-properties-and-tags"></a>Свойства запросов и теги

В рамках эксперимента можно заставить запрос вернуть список запусков, которые соответствуют определенным свойствам и тегам.

#### <a name="using-the-sdk"></a>Использование пакета SDK

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>Использование интерфейса командной строки

Azure CLI поддерживает запросы [JMESPath,](http://jmespath.org) которые могут использоваться для фильтрации запусков на основе свойств и тегов. Чтобы использовать запрос JMESPath с Azure CLI, `--query` укажите его с параметром. Следующие примеры показывают основные запросы с использованием свойств и тегов:

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

Для получения дополнительной информации о результатах [Query Azure CLI command output](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest)запроса Azure CLI см.

### <a name="using-azure-machine-learning-studio"></a>Использование студии машинного обучения Azure

1. Перейдите в раздел **Трубопроводы.**

1. Используйте панель поиска для фильтрации конвейеров, используя теги, описания, имена экспериментов и имя отправителя.

## <a name="example-notebooks"></a>Примеры записных книжек

Следующие блокноты демонстрируют концепции в этой статье:

* Чтобы узнать больше о опийных аПОХ, см. [logging API notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb)

* Для получения дополнительной информации об управлении выполняетс с [manage runs notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb)Azure machine Learning SDK, см.

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать, как войти метрики для ваших экспериментов, см [Лог метрики во время учебных запусков](how-to-track-experiments.md).
* Чтобы узнать, как контролировать ресурсы и журналы из машинного обучения Azure, смотрите [Мониторинг машинного обучения Azure.](monitor-azure-machine-learning.md)