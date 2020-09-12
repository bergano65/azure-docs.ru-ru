---
title: Отправка обучающего выполнения в целевой объект вычислений
titleSuffix: Azure Machine Learning
description: Обучение модели машинного обучения в различных средах обучения (целевые объекты вычислений). Вы можете с легкостью переключаться между средами обучения. Начните обучение на локальном компьютере. Если вам потребуется горизонтально увеличить масштаб, переключитесь на облачный целевой объект вычислений.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: da48b593b8f645566b2f9775fabc5d8e62e625b6
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661573"
---
# <a name="submit-a-training-run-to-a-compute-target"></a>Отправка обучающего выполнения в целевой объект вычислений

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Из этой статьи вы узнаете, как использовать различные среды обучения ([целевые объекты вычислений](concept-compute-target.md)) для обучения модели машинного обучения.

При обучении этот учебный сценарий обычно запускают на локальном компьютере, а затем запускают в другом целевом объекте вычислений. С помощью Машинное обучение Azure сценарий можно выполнять в различных целевых объектах вычислений, не меняя сценарий обучения.

Все, что нужно сделать, — это определить среду для каждого целевого объекта вычислений в **конфигурации запуска сценария**.  Затем, если вы хотите запустить обучающий эксперимент на другом целевом объекте вычислений, укажите конфигурацию запуска для этого вычисления.

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начинать работу. Опробуйте [бесплатную или платную версию машинное обучение Azure](https://aka.ms/AMLFree) уже сегодня
* [Пакет SDK для машинное обучение Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)
* [Рабочая область машинное обучение Azure](how-to-manage-workspace.md)`ws`
* Целевой объект вычислений, `my_compute_target` .  Создать целевой объект вычислений с помощью:
  * [Пакет SDK для Python](how-to-create-attach-compute-sdk.md) 
  * [Студия машинного обучения Azure.](how-to-create-attach-compute-studio.md)

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>Что такое конфигурация запуска сценария?

Вы отправляете обучающий эксперимент с помощью объекта [скриптрунконфиг](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) .  Этот объект включает в себя следующее.

* **source_directory**: исходный каталог, который содержит сценарий обучения.
* **script**: определение сценария обучения.
* **run_config**: [Конфигурация запуска](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py&preserve-view=true), которая, в свою очередь, определяет, где будет выполняться обучение. В `run_config` задается целевой объект вычислений и среда, используемая при запуске сценария обучения.  

## <a name="whats-an-environment"></a>Что такое среда?

Машинное обучение Azure [средах](concept-environments.md) — это инкапсуляция среды, в которой выполняется обучение машинного обучения. Они указывают пакеты Python, переменные среды и параметры программного обеспечения для сценариев обучения и оценки. Они также указывают время выполнения (Python, Spark или DOCKER).  

Среды указываются в  `run_config` объекте внутри `ScriptRunConfig` .

## <a name="train-your-model"></a><a id="submit"></a>Обучение модели

Шаблон кода для отправки запуска на выполнение обучения одинаковый для всех типов целевых объектов вычислений.

1. Создание эксперимента для запуска
1. Создание среды, в которой будет выполняться скрипт
1. Создание конфигурации запуска сценария, которая ссылается на целевой объект вычислений и среду
1. Выполнение прогона
1. Дождитесь завершения выполнения

Также вы можете:

* отправить эксперимент с объектом `Estimator`, как показано в разделе [Обучение моделей машинного обучения с использованием средства оценки](how-to-train-ml-models.md);
* Отправьте запрос на запуск HyperDrive для [настройки гиперпараметров](how-to-tune-hyperparameters.md).
* Отправьте эксперимент с помощью расширения [VS Code](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

## <a name="create-an-experiment"></a>Создание эксперимента

Создайте эксперимент в рабочей области.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'

experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="create-an-environment"></a>Создание среды

Проверенные среды содержат коллекции пакетов Python и доступны в рабочей области по умолчанию. Эти среды поддерживаются кэшированными образами DOCKER, что сокращает затраты на подготовку к запуску. Для удаленного целевого объекта вычислений можно использовать одну из распространенных проверенных сред, чтобы начать с:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
my_environment = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Дополнительные сведения и сведения о средах см. [в разделе создание & использование программных сред в машинное обучение Azure](how-to-use-environments.md).
  
### <a name="local-compute-target"></a>Локальный целевой объект вычислений

Если целевым объектом вычислений является **локальный компьютер**, вы несете ответственность за обеспечение доступности всех необходимых пакетов в среде Python, в которой выполняется сценарий.  Используйте `python.user_managed_dependencies` для использования текущей среды Python (или Python по указанному пути).

```python
from azureml.core import Environment

# Editing a run configuration property on-fly.
my_environment = Environment("user-managed-env")

my_environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#my_environment.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-script-run-configuration"></a>Создать конфигурацию запуска скрипта

Теперь, когда у вас есть целевой объект вычислений ( `compute_target` ) и среда ( `my_environment` ), создайте конфигурацию запуска сценария, которая запускает сценарий обучения ( `train.py` ), расположенный в `project_folder` каталоге:

```python
from azureml.core import ScriptRunConfig

script_run_config = ScriptRunConfig(source_directory=project_folder, script='train.py')

# Set compute target
script_run_config.run_config.target = my_compute_target

# Set environment.   If you don't do this, a default environment will be created.
script_run_config.run_config.environment = my_environment
```

Также может потребоваться задать платформу для запуска.

* Для кластера HDI:
    ```python
    src.run_config.framework = "pyspark"
    ```

* Для удаленной виртуальной машины:
    ```python
    src.run_config.framework = "python"
    ```

## <a name="submit-the-experiment"></a>Отправка эксперимента

```python
run = experiment.submit(config=script_run_config)
```

> [!IMPORTANT]
> При отправке запроса на запуск выполнения обучения создается моментальный снимок каталога, содержащего сценарии обучения, и отправляется на целевой объект вычислений. Он также хранится в составе эксперимента в рабочей области. Если изменить файлы и отправить запрос на запуск выполнения снова, будут переданы только измененные файлы.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Дополнительные сведения о моментальных снимках см. в разделе [моментальные снимки](concept-azure-machine-learning-architecture.md#snapshots).


<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Отслеживание и интеграция Git

При выполнении запуска обучения, в котором исходный каталог является локальным репозиторием Git, сведения о репозитории хранятся в журнале выполнения. Дополнительные сведения см. в статье [Интеграция с Git для Машинного обучения Azure](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a>Примеры записных книжек

Посмотрите примеры обучения различных целевых объектов вычислений в следующих записных книжках.
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Руководство. по обучению модели классификации изображений с помощью Службы машинного обучения Azure](tutorial-train-models-with-aml.md). В нем используется управляемый целевой объект вычислений для обучения модели.
* Узнайте, как [эффективно настроить параметры](how-to-tune-hyperparameters.md) для создания лучших моделей. View = Azure-ML-корректировка&сохранить-View = true)
* После обучения модели узнайте о [способах и расположениях развертывания моделей](how-to-deploy-and-where.md).
* Обзор справочника по пакету SDK [класса RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py&preserve-view=true).
* [Использование Машинного обучения Azure с виртуальными сетями Microsoft Azure](how-to-enable-virtual-network.md)