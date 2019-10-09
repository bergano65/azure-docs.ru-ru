---
title: Запуск, отслеживание и отмена обучающих запусков в Python
titleSuffix: Azure Machine Learning
description: Узнайте, как начать, задать состояние, отмечать и упорядочивать эксперименты машинного обучения.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2019
ms.openlocfilehash: 7ebbc7575ad52bbf7a399babb048113bc505a7f8
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174534"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Запуск, отслеживание и отмена обучающих запусков в Python

[Пакет SDK для машинное обучение Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) и [машинное обучение CLI](reference-azure-machine-learning-cli.md) предоставляет различные методы мониторинга, Организации и управления запусками для обучения и экспериментирования.

В этой статье приведены примеры следующих задач.

* Мониторинг производительности выполнения.
* Отмена или сбой выполнения.
* Создание дочерних запусков.
* Теги и поиск запусков.

## <a name="prerequisites"></a>Предварительные требования

Вам потребуются следующие элементы:

* Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree) уже сегодня.

* [Рабочая область машинное обучение Azure](how-to-manage-workspace.md).

* Машинное обучение Azure SDK для Python (версия 1.0.21 или более поздняя). Чтобы установить или обновить последнюю версию пакета SDK, см. статью [Установка или обновление пакета SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

    Чтобы проверить версию пакета SDK для Машинное обучение Azure, используйте следующий код:

    ```python
    print(azureml.core.VERSION)
    ```

* Расширение [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) и [CLI для машинное обучение Azure](reference-azure-machine-learning-cli.md).

## <a name="start-a-run-and-its-logging-process"></a>Запуск запуска и процесса ведения журнала

### <a name="using-the-sdk"></a>Использование пакета SDK

Настройте эксперимент, импортировав классы [рабочей области](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py), [эксперимент](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py), [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)и [скриптрунконфиг](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) из пакета [azureml. Core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py) .

```python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Запустите запуск и процесс ведения журнала с помощью метода [`start_logging()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) .

```python
notebook_run = exp.start_logging()
notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>Использование интерфейса командной строки

Чтобы начать выполнение эксперимента, выполните следующие действия.

1. В оболочке или командной строке используйте Azure CLI для проверки подлинности в подписке Azure:

    ```azurecli-interactive
    az login
    ```

1. Подключите конфигурацию рабочей области к папке, содержащей сценарий обучения. Замените `myworkspace` рабочей областью Машинное обучение Azure. Замените `myresourcegroup` группой ресурсов Azure, которая содержит рабочую область:

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Эта команда создает подкаталог `.azureml`, который содержит примеры файлов среды runconfig и conda. Он также содержит файл `config.json`, который используется для взаимодействия с рабочей областью Машинное обучение Azure.

    Дополнительные сведения см. в разделе [AZ ML Folder Attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

2. Чтобы начать выполнение, используйте следующую команду. При использовании этой команды укажите имя файла runconfig (текст перед @no__t -0. runconfig, если вы ищете файловую систему) в параметре-c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > Команда `az ml folder attach` создала подкаталог `.azureml`, который содержит два примера файлов runconfig.
    >
    > При наличии скрипта Python, который создает объект конфигурации запуска программно, можно использовать [RunConfig. Save ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) , чтобы сохранить его как файл RunConfig.
    >
    > Дополнительные примеры файлов runconfig см. в разделе [https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml).

    Дополнительные сведения см. в разделе [AZ ML Run Submit-Script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

## <a name="monitor-the-status-of-a-run"></a>Наблюдение за состоянием запуска

### <a name="using-the-sdk"></a>Использование пакета SDK

Получение состояния запуска с помощью метода [`get_status()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) .

```python
print(notebook_run.get_status())
```

Чтобы получить идентификатор выполнения, время выполнения и дополнительные сведения о выполнении, используйте метод [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) .

```python
print(notebook_run.get_details())
```

После успешного завершения выполнения используйте метод [`complete()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) , чтобы пометить его как завершенный.

```python
notebook_run.complete()
print(notebook_run.get_status())
```

Если вы используете шаблон проектирования `with...as` Python, выполнение автоматически помечается как завершенное, если выполнение выходит за пределы области. Не нужно вручную помечать запуск как завершенный.

```python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print(notebook_run.get_status())

print(notebook_run.get_status())
```

### <a name="using-the-cli"></a>Использование интерфейса командной строки

1. Чтобы просмотреть список запусков для эксперимента, используйте следующую команду. Замените `experiment` именем своего эксперимента:

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    Эта команда возвращает документ JSON, в котором перечисляются сведения о выполнении этого эксперимента.

    Дополнительные сведения см. в статье [AZ ML эксперимент List](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

2. Чтобы просмотреть сведения о конкретном выполнении, используйте следующую команду. Замените `runid` ИДЕНТИФИКАТОРом запуска:

    ```azurecli-interactive
    az ml run show -r runid
    ```

    Эта команда возвращает документ JSON, в котором перечисляются сведения о выполнении.

    Дополнительные сведения см. в разделе [AZ ML Run показ](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).

## <a name="cancel-or-fail-runs"></a>Отмена или неудача выполнения

Если вы заметили ошибку или если выполнение занимает слишком много времени, можно отменить запуск.

### <a name="using-the-sdk"></a>Использование пакета SDK

Чтобы отменить запуск с помощью пакета SDK, используйте метод [`cancel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) :

```python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_script_run = exp.submit(run_config)
print(local_script_run.get_status())

local_script_run.cancel()
print(local_script_run.get_status())
```

Если выполнение завершается, но содержит ошибку (например, был использован неверный сценарий обучения), можно использовать метод [`fail()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)#fail-error-details-none--error-code-none---set-status-true-) , чтобы пометить его как неудачный.

```python
local_script_run = exp.submit(run_config)
local_script_run.fail()
print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>Использование интерфейса командной строки

Чтобы отменить запуск с помощью интерфейса командной строки, используйте следующую команду. Замените `runid` ИДЕНТИФИКАТОРом запуска.

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Дополнительные сведения см. в статье [AZ ML Run отмена](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

## <a name="create-child-runs"></a>Создание дочерних запусков

Создайте дочерние выполнения для объединения связанных запусков, например для различных итераций настройки параметров.

> [!NOTE]
> Дочерние запуски можно создать только с помощью пакета SDK.

В этом примере кода используется скрипт `hello_with_children.py` для создания пакета из пяти дочерних запусков из отправленного запуска с помощью метода [`child_run()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) :

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
> При выходе из области действия дочерние тесты автоматически помечаются как завершенные.

Чтобы эффективно создать множество дочерних запусков, используйте метод [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) . Поскольку каждое создание приводит к сетевому вызову, создание пакета выполняется более эффективно, чем создание их по одному.

### <a name="submit-child-runs"></a>Отправить дочерние выполнения

Дочерние запуски также могут быть отправлены из родительского запуска. Это позволяет создавать иерархии родительских и дочерних приложений, каждый из которых работает в разных целевых объектах вычислений, Соединенных общим родительским ИДЕНТИФИКАТОРом запуска.

Используйте метод ["submit_child ()"](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#submit-child-config--tags-none----kwargs-) для отправки дочернего выполнения из родительского запуска. Чтобы сделать это в родительском скрипте выполнения, получите контекст выполнения и отправьте дочерний запрос, используя метод ``submit_child`` экземпляра контекста.

```python
## In parent run script
parent_run = Run.get_context()
child_run_config = ScriptRunConfig(source_directory='.', script='child_script.py')
parent_run.submit_child(child_run_config)
```

В дочернем выполнении можно просмотреть идентификатор родительского запуска:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Дочерние выполнения запросов

Чтобы запросить дочерние выполнения определенного родителя, используйте метод [`get_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) . Аргумент ``recursive = True`` позволяет запрашивать вложенное дерево дочерних элементов и внуками.

```python
print(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>Теги и поиск запусков

В Машинное обучение Azure можно использовать свойства и теги для упорядочения и запроса своих запусков для получения важной информации.

### <a name="add-properties-and-tags"></a>Добавление свойств и тегов

#### <a name="using-the-sdk"></a>Использование пакета SDK

Чтобы добавить метаданные для поиска в запуски, используйте метод [`add_properties()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) . Например, следующий код добавляет свойство `"author"` к выполнению:

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Свойства являются неизменяемыми, поэтому они создают постоянную запись для целей аудита. В следующем примере кода возникает ошибка, так как мы уже добавили `"azureml-user"` в качестве значения свойства `"author"` в приведенном выше коде:

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

В отличие от свойств, теги являются изменяемыми. Чтобы добавить доступную для поиска и осмысленную информацию для потребителей вашего эксперимента, используйте метод [`tag()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) .

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Можно также добавить простые строковые Теги. Если эти теги отображаются в словаре тегов в качестве ключей, они имеют значение `None`.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>Использование интерфейса командной строки

> [!NOTE]
> С помощью интерфейса командной строки можно добавлять или обновлять только теги.

Чтобы добавить или обновить тег, используйте следующую команду:

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

Дополнительные сведения см. в разделе [AZ ML Run Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update).

### <a name="query-properties-and-tags"></a>Свойства и Теги запроса

Вы можете запросить запуски в эксперименте, чтобы получить список запусков, соответствующих указанным свойствам и тегам.

#### <a name="using-the-sdk"></a>Использование пакета SDK

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>Использование интерфейса командной строки

Azure CLI поддерживает запросы [JMESPath](http://jmespath.org) , которые можно использовать для фильтрации запусков на основе свойств и тегов. Чтобы использовать запрос JMESPath с Azure CLI, укажите его с помощью параметра `--query`. В следующих примерах показаны основные запросы, использующие свойства и теги.

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

Дополнительные сведения о запросах Azure CLI результатов см. в разделе [запрос Azure CLI команды Output](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).

## <a name="example-notebooks"></a>Примеры записных книжек

В следующих записных книжках демонстрируются концепции, описанные в этой статье.

* Дополнительные сведения об API ведения журнала см. в [записной книжке API для ведения журнала](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb).

* Дополнительные сведения об управлении запусками с помощью пакета SDK для Машинное обучение Azure см. в разделе [Управление запуском записной книжки](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb).

## <a name="next-steps"></a>Следующие шаги

* Сведения о том, как регистрировать метрики для экспериментов, см. в статье [метрики журнала во время учебных запусков](how-to-track-experiments.md).
