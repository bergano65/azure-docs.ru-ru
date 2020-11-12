---
title: Настройка обучающего запуска
titleSuffix: Azure Machine Learning
description: Обучение модели машинного обучения в различных средах обучения (целевые объекты вычислений). Вы можете с легкостью переключаться между средами обучения.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: cb10eb0f89ce37bc484c8570995ebaa098c696f1
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541306"
---
# <a name="configure-and-submit-training-runs"></a>Настройка и отправка запуска на выполнение обучения

Из этой статьи вы узнаете, как настроить и отправить Машинное обучение Azure запуски для обучения моделей.

При обучении обычно запускается на локальном компьютере, а затем масштабируется в облачный кластер. С помощью Машинное обучение Azure сценарий можно выполнять в различных целевых объектах вычислений, не меняя сценарий обучения.

Все, что нужно сделать, — это определить среду для каждого целевого объекта вычислений в **конфигурации запуска сценария**.  Затем, если вы хотите запустить обучающий эксперимент на другом целевом объекте вычислений, укажите конфигурацию запуска для этого вычисления.

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начинать работу. Опробуйте [бесплатную или платную версию машинное обучение Azure](https://aka.ms/AMLFree) уже сегодня
* [Пакет SDK для машинное обучение Azure для Python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.13.0)
* [Рабочая область машинное обучение Azure](how-to-manage-workspace.md)`ws`
* Целевой объект вычислений, `my_compute_target` .  [Создание целевого объекта вычислений](how-to-create-attach-compute-studio.md) 

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>Что такое конфигурация запуска сценария?
[Скриптрунконфиг](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) используется для настройки сведений, необходимых для отправки обучающего запуска в рамках эксперимента.

Вы отправляете обучающий эксперимент с помощью объекта Скриптрунконфиг.  Этот объект включает в себя следующее.

* **source_directory** : исходный каталог, который содержит сценарий обучения.
* **Скрипт** : обучающий сценарий для выполнения
* **compute_target** : целевой объект вычислений, на котором выполняется
* **Среда** : среда, используемая при выполнении скрипта
* и некоторые дополнительные настраиваемые параметры (Дополнительные сведения см. в [справочной документации](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) ).

## <a name="train-your-model"></a><a id="submit"></a>Обучение модели

Шаблон кода для отправки запуска на выполнение обучения одинаковый для всех типов целевых объектов вычислений.

1. Создание эксперимента для запуска
1. Создание среды, в которой будет выполняться скрипт
1. Создайте Скриптрунконфиг, который указывает целевой объект вычислений и среду.
1. Выполнение прогона
1. Дождитесь завершения выполнения

Также вы можете:

* Отправьте запрос на запуск HyperDrive для [настройки гиперпараметров](how-to-tune-hyperparameters.md).
* Отправьте эксперимент с помощью расширения [VS Code](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

## <a name="create-an-experiment"></a>Создание эксперимента

Создайте эксперимент в рабочей области.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'
experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="select-a-compute-target"></a>Выберите целевой объект вычислений

Выберите целевой объект вычислений, на котором будет выполняться сценарий обучения. Если в Скриптрунконфиг не указан целевой объект вычислений или `compute_target='local'` , Azure ML будет выполнять скрипт локально. 

В примере кода в этой статье предполагается, что вы уже создали целевой объект вычислений `my_compute_target` из раздела "необходимые условия".

## <a name="create-an-environment"></a>Создание среды
Машинное обучение Azure [средах](concept-environments.md) — это инкапсуляция среды, в которой выполняется обучение машинного обучения. Они указывают пакеты Python, образ DOCKER, переменные среды и параметры программного обеспечения для сценариев обучения и оценки. Они также указывают среды выполнения (Python, Spark или DOCKER).

Можно либо определить собственную среду, либо использовать проверенную среду машинного обучения Azure. [Проверенные среды](./how-to-use-environments.md#use-a-curated-environment) — это стандартные среды, доступные в рабочей области по умолчанию. Эти среды поддерживаются кэшированными образами DOCKER, что сокращает затраты на подготовку к запуску. Полный список доступных проверенных сред см. в разделе [машинное обучение Azure проверенные среды](./resource-curated-environments.md) .

Для удаленного целевого объекта вычислений можно использовать одну из распространенных проверенных сред, чтобы начать с:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Дополнительные сведения и сведения о средах см. [в разделе создание & использование программных сред в машинное обучение Azure](how-to-use-environments.md).
  
### <a name="local-compute-target"></a><a name="local"></a>Локальный целевой объект вычислений

Если целевым объектом вычислений является **локальный компьютер** , вы несете ответственность за обеспечение доступности всех необходимых пакетов в среде Python, в которой выполняется сценарий.  Используйте `python.user_managed_dependencies` для использования текущей среды Python (или Python по указанному пути).

```python
from azureml.core import Environment

myenv = Environment("user-managed-env")
myenv.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
# myenv.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-the-script-run-configuration"></a>Создание конфигурации запуска скрипта

Теперь, когда у вас есть целевой объект вычислений ( `my_compute_target` ) и среда ( `myenv` ), создайте конфигурацию запуска сценария, которая запускает сценарий обучения ( `train.py` ), расположенный в `project_folder` каталоге:

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='train.py',
                      compute_target=my_compute_target,
                      environment=myenv)

# Set compute target
# Skip this if you are running on your local computer
script_run_config.run_config.target = my_compute_target
```

Если среда не указана, будет создана среда по умолчанию.

Если у вас есть аргументы командной строки, которые необходимо передать в сценарий обучения, их можно указать с помощью **`arguments`** параметра конструктора скриптрунконфиг, например `arguments=['--arg1', arg1_val, '--arg2', arg2_val]` .

Если вы хотите переопределить максимально допустимое для выполнения значение по умолчанию, это можно сделать с помощью **`max_run_duration_seconds`** параметра. Система попытается автоматически отменить выполнение, если оно занимает больше времени, чем это значение.

### <a name="specify-a-distributed-job-configuration"></a>Укажите конфигурацию распределенного задания
Если вы хотите выполнить распределенное задание обучения, предоставьте параметру конфигурацию распределенного задания **`distributed_job_config`** . Поддерживаются следующие типы конфигурации: [мпиконфигуратион](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py), [тенсорфловконфигуратион](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?preserve-view=true&view=azure-ml-py)и [питорчконфигуратион](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?preserve-view=true&view=azure-ml-py). 

Дополнительные сведения и примеры выполнения распределенных заданий хоровод, TensorFlow и PyTorch см. в следующих статьях:

* [Обучение моделей TensorFlow](./how-to-train-tensorflow.md#distributed-training)
* [Обучение моделей PyTorch](./how-to-train-pytorch.md#distributed-training)

## <a name="submit-the-experiment"></a>Отправка эксперимента

```python
run = experiment.submit(config=src)
run.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> При отправке запроса на запуск выполнения обучения создается моментальный снимок каталога, содержащего сценарии обучения, и отправляется на целевой объект вычислений. Он также хранится в составе эксперимента в рабочей области. Если изменить файлы и отправить запрос на запуск выполнения снова, будут переданы только измененные файлы.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Дополнительные сведения о моментальных снимках см. в разделе [моментальные снимки](concept-azure-machine-learning-architecture.md#snapshots).

> [!IMPORTANT]
> **Особые папки**. Две папки — *выходные данные* и *журналы* , обрабатываются Машинным обучением Azure специальным образом. Если вы записываете файлы в папки с именами *выходные данные* и *журналы* , которые относятся к корневому каталогу (`./outputs` и `./logs` соответственно), то эти файлы во время обучения автоматически будут отправляться в журнал выполнения, чтобы после завершения выполнения у вас был к ним доступ.
>
> Чтобы создать артефакты во время обучения (например, к файлам моделей, контрольным точкам, файлам данных или графическим изображениям), запишите их в папку `./outputs`.
>
> Аналогичным образом можно записать любые журналы выполнения обучения в папку `./logs`. Чтобы использовать [интеграцию TensorBoard](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/tensorboard/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) Машинного обучения Azure, убедитесь, что вы записываете журналы TensorBoard в эту папку. Во время выполнения вы сможете запустить TensorBoard и выполнить потоковую передачу этих журналов.  Позже вы также сможете восстановить журналы из любого из ваших предыдущих запусков.
>
> Например, чтобы загрузить файл, записанный в папку *выходные данные* локального компьютера, после запуска удаленного обучения выполните такую команду: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

## <a name="git-tracking-and-integration"></a><a id="gitintegration"></a>Отслеживание и интеграция Git

При выполнении запуска обучения, в котором исходный каталог является локальным репозиторием Git, сведения о репозитории хранятся в журнале выполнения. Дополнительные сведения см. в статье [Интеграция с Git для Машинного обучения Azure](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a>Примеры записных книжек

В этих записных книжках приведены примеры настройки запусков для различных сценариев обучения.
* [Обучение различным целям вычислений](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Обучение с помощью платформ ML](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Руководство. по обучению модели классификации изображений с помощью Службы машинного обучения Azure](tutorial-train-models-with-aml.md). В нем используется управляемый целевой объект вычислений для обучения модели.
* Узнайте, как обучить модели с помощью конкретных платформ машинного обучения, таких как [Scikit-](how-to-train-scikit-learn.md)Learning, [TensorFlow](how-to-train-tensorflow.md)и [PyTorch](how-to-train-pytorch.md).
* Узнайте, как [эффективно настроить гиперпараметры](how-to-tune-hyperparameters.md) для создания улучшенных моделей.
* После обучения модели узнайте о [способах и расположениях развертывания моделей](how-to-deploy-and-where.md).
* Просмотрите ссылку на пакет SDK для [класса скриптрунконфиг](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) .
* [Использование Машинного обучения Azure с виртуальными сетями Microsoft Azure](./how-to-network-security-overview.md)