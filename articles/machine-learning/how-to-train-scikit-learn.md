---
title: Обучение scikit. изучение моделей машинного обучения
titleSuffix: Azure Machine Learning
description: Узнайте, как выполнять сценарии обучения scikit-учиться на Машинное обучение Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 07/24/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: d023fa5786375f8fb4dbcdfe01e32da0400088f8
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87849398"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Создание scikit. изучение моделей в масштабе с помощью Машинное обучение Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье вы узнаете, как выполнять scikitные сценарии обучения с помощью Машинное обучение Azure.

Примеры сценариев, приведенные в этой статье, используются для классификации изображений диафрагмы для создания модели машинного обучения на основе [набора данных диафрагмы](https://archive.ics.uci.edu/ml/datasets/iris)scikit.

Если вы научитесь изучать модель машинного обучения scikit-учиться с нуля или используете существующую модель в облаке, вы можете использовать Машинное обучение Azure для масштабирования заданий обучения с открытым исходным кодом с помощью эластичных облачных ресурсов. Вы можете создавать, развертывать, выполнять версии и отслеживать модели производственного уровня с помощью Машинное обучение Azure.

## <a name="prerequisites"></a>Предварительные требования

Запустите этот код в любой из этих сред:
 - Вычислительная операция Машинного обучения Azure — загрузка или установка не требуется

    - Выполните инструкции из [учебника Настройка среды и рабочей области](tutorial-1st-experiment-sdk-setup.md) , чтобы создать выделенный сервер записной книжки, предварительно загруженный с помощью пакета SDK и примера репозитория.
    - В папке Learning Samples на сервере записной книжки найдите готовую и развернутую записную книжку, перейдя к этому каталогу: **практические советы по использованию azureml > ML-frameworks > scikit-learning > learning > Train-i Parameter-Learning-Deploy-with-sklearn** Folder.

 - Собственный сервер Jupyter Notebook

    - [Установите пакет SDK для машинное обучение Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Создайте файл конфигурации рабочей области](how-to-configure-environment.md#workspace).

## <a name="set-up-the-experiment"></a>Настройка эксперимента

В этом разделе выполняется настройка обучающего эксперимента путем загрузки требуемых пакетов Python, инициализации рабочей области, создания эксперимента и отправки обучающих данных и сценариев обучения.

### <a name="initialize-a-workspace"></a>Инициализация рабочей области

[Машинное обучение Azure Рабочая область](concept-workspace.md) — это ресурс верхнего уровня для службы. Она предоставляет централизованное расположение для работы со всеми создаваемыми артефактами. В пакете SDK для Python можно получить доступ к артефактам рабочей области, создав [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) объект.

Создайте объект рабочей области из `config.json` файла, созданного в [разделе Предварительные требования](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```


### <a name="prepare-scripts"></a>Подготовка скриптов

В этом руководстве обучающий сценарий **train_iris. Корректировка** уже предоставлен [здесь](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py). На практике вы можете использовать любой пользовательский сценарий обучения как есть и запускать его с помощью Azure ML без необходимости изменять код.

Примечания.
- В предоставленном сценарии обучения показано, как регистрировать некоторые метрики в МАШИНном обучении Azure с помощью `Run` объекта в скрипте.
- Предоставленный Скрипт обучения использует примеры данных из `iris = datasets.load_iris()` функции.  Для собственных данных может потребоваться выполнить такие действия, как [Передача набора данных и скриптов](how-to-train-keras.md#data-upload) , чтобы сделать данные доступными во время обучения.

### <a name="define-your-environment"></a>Определите среду.

#### <a name="create-a-custom-environment"></a>Создайте пользовательскую среду.

Создайте среду conda (склеарн-ЕНВ. yml).
Чтобы записать среду conda из записной книжки, можно добавить строку ```%%writefile sklearn-env.yml``` в верхней части ячейки.

```yaml
name: sklearn-training-env
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

Создайте среду машинного обучения Azure из этой спецификации среды Conda. Среда будет упакована в контейнер DOCKER во время выполнения.
```python
from azureml.core import Environment

myenv = Environment.from_conda_specification(name = "myenv", file_path = "sklearn-env.yml")
```

#### <a name="use-a-curated-environment"></a>Использование проверенной среды
Azure ML предоставляет готовые, проверенные среды контейнеров, если вы не хотите создавать собственный образ. Дополнительные сведения см. [здесь](resource-curated-environments.md).
Если вы хотите использовать проверенную среду, то вместо этого можно выполнить следующую команду:

```python
env = Environment.get(workspace=ws, name="AzureML-Tutorial")
```

### <a name="create-a-scriptrunconfig"></a>Создание Скриптрунконфиг

Эта Скриптрунконфиг отправит задание для выполнения на локальном целевом объекте вычислений.

```python
from azureml.core import ScriptRunConfig

sklearnconfig = ScriptRunConfig(source_directory='.', script='train_iris.py')
src.run_config.environment = myenv
```

Если вы хотите отправить в удаленный кластер, можно изменить run_config. Target на требуемый целевой объект вычислений.

### <a name="submit-your-run"></a>Отправка выполнения
```python
from azureml.core import Experiment

run = Experiment(ws,'train-sklearn').submit(config=sklearnconfig)
run.wait_for_completion(show_output=True)

```

> [!WARNING]
> Машинное обучение Azure запускает скрипты обучения, копируя весь исходный каталог. Если у вас есть конфиденциальные данные, которые не нужно передавать, используйте [файл. Ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) или не включайте его в исходный каталог. Вместо этого получите доступ к данным с помощью [хранилища](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)данных.

Дополнительные сведения о настройке среды Python см. на [этой странице](how-to-use-environments.md). 

## <a name="what-happens-during-run-execution"></a>Что происходит во время выполнения
При выполнении выполнения он проходит следующие этапы.

- **Подготовка**. образ DOCKER создается в соответствии с оценкой TensorFlow. Образ отправляется в реестр контейнеров рабочей области и кэшируется для последующего выполнения. Журналы также передаются в журнал выполнения и могут быть просмотрены для отслеживания хода выполнения.

- **Масштабирование**. Кластер пытается выполнить масштабирование, если кластеру Batch AI требуется больше узлов для выполнения выполнения, чем в настоящее время доступно.

- **Выполняется**: все скрипты в папке Script передаются в целевой объект вычислений, хранилища данных подключаются или копируются, а entry_script выполняется. Выходные данные из STDOUT и папки/логс передаются в журнал выполнения и могут использоваться для наблюдения за выполнением.

- **Пост-обработка**. папка/Outputs для выполнения копируется в журнал выполнения.

## <a name="save-and-register-the-model"></a>Сохранение и регистрация модели

После обучения модель можно сохранить и зарегистрировать в рабочей области. Регистрация модели позволяет хранить и редактировать версии моделей в рабочей области для упрощения [развертывания и управления моделями](concept-model-management-and-deployment.md).

Добавьте следующий код в сценарий обучения train_iris. корректировки, чтобы сохранить модель. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Зарегистрируйте модель в рабочей области с помощью следующего кода. При указании параметров `model_framework` , `model_framework_version` и `resource_configuration` , развертывание модели без кода станет доступным. Развертывание модели без кода позволяет напрямую развернуть модель в качестве веб-службы из зарегистрированной модели, а [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py) объект определяет ресурс вычислений для веб-службы.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Развертывание

Только что зарегистрированная модель может быть развернута точно так же, как и любая другая Зарегистрированная модель в Машинное обучение Azure, независимо от того, какой механизм оценки использовался для обучения. Руководство по развертыванию содержит раздел, посвященный регистрации моделей, но можно сразу перейти к [созданию целевого объекта вычислений](how-to-deploy-and-where.md#choose-a-compute-target) для развертывания, так как у вас уже есть Зарегистрированная модель.

### <a name="preview-no-code-model-deployment"></a>Образца Развертывание модели без кода

Вместо традиционного маршрута развертывания можно также использовать функцию развертывания без кода (Предварительная версия) для scikit — обучение. Развертывание модели без кода поддерживается для всех встроенных типов моделей scikit-учиться. Зарегистрировав модель, как показано выше с помощью `model_framework` параметров, `model_framework_version` и `resource_configuration` , можно просто использовать [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) статическую функцию для развертывания модели.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

Примечание. Эти зависимости включены в предварительно построенный контейнер вывода scikit-учиться.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

Полное [Описание процесса](how-to-deploy-and-where.md) развертывания в машинное обучение Azure более подробно.


## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы обучили и зарегистрировали модель scikit-учиться и узнали о вариантах развертывания. Дополнительные сведения о Машинное обучение Azure см. в других статьях.

* [Отслеживание метрик выполнения во время обучения](how-to-track-experiments.md)
* [Настройка гиперпараметров](how-to-tune-hyperparameters.md)
* [Эталонная архитектура для распределенного обучения глубокого обучения в Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
