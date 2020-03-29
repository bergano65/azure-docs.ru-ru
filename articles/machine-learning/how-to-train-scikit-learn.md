---
title: Поезд scikit-учиться модели машинного обучения
titleSuffix: Azure Machine Learning
description: Узнайте, как запускать скрипты обучения scikit в корпоративном масштабе, используя класс оценки SKlearn Поступен для машин Azure. Пример скриптов классифицировать изображения цветка радужной оболочки глаза для создания модели машинного обучения на основе scikit-учиться радужной оболочки данных.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: bdd2cc400c3df75742689258caea8cb87ee8ccc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942275"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Создавайте модели scikit-learn в масштабе с помощью машинного обучения Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье узнайте, как запускать скрипты обучения scikit в корпоративном масштабе, используя класс [оценки SKlearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) Azure Machine Learning. 

Примеры скриптов в этой статье используются для классификации изображений цветка радужной оболочки глаза для создания модели машинного обучения на основе набора данных scikit-learn' [радужной оболочки глаза.](https://archive.ics.uci.edu/ml/datasets/iris)

Независимо от того, обучаете ли вы модель машинного обучения scikit-learn с нуля или приносите существующую модель в облако, вы можете использовать Azure Machine Learning для масштабирования рабочих мест с открытым исходным кодом с использованием эластичных облачных вычислительных ресурсов. Вы можете создавать, развертывать, изготавливать и контролировать производственные модели с помощью машинного обучения Azure.

## <a name="prerequisites"></a>Предварительные требования

Выполнить этот код на любой из этих сред:
 - Экземпляр вычислений машинного обучения Azure - нет необходимости загружать или установку

    - Завершите [обучение: Настройка среды и рабочего пространства](tutorial-1st-experiment-sdk-setup.md) для создания специального ноутбука сервера предварительно загружены с SDK и образец репозитория.
    - В папке для обучения образцов на блокноте-сервере найдите завершенный и расширенный блокнот, перепрыгивая в этот каталог: **как-к-использованию-azureml > мл-рамки > scikit-learn > обучение > поезд-гиперпараметр-настройка-развертывание-с-sklearn** папку.

 - Собственный сервер Jupyter Notebook

    - [Установите SDK машинного обучения Azure.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
    - [Создание файла конфигурации рабочего пространства.](how-to-configure-environment.md#workspace)
    - Загрузить набор данных и пример файла скрипта 
        - [набор данных радужной оболочки глаза](https://archive.ics.uci.edu/ml/datasets/iris)
        - [train_iris.py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn)
    - Вы также можете найти завершенную [версию Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) этого руководства на странице образцов GitHub. Ноутбук включает в себя расширенный раздел, охватывающий интеллектуальный гиперпараметр настройки и извлечения лучшей модели по основным метрикам.

## <a name="set-up-the-experiment"></a>Настройка эксперимента

В этом разделе устанавливается учебный эксперимент путем загрузки необходимых пакетов питона, инициализации рабочего пространства, создания эксперимента и загрузки обучающих данных и обучающих скриптов.

### <a name="import-packages"></a>Импорт пакетов

Во-первых, импортируйте необходимые библиотеки Python.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Инициализация рабочего пространства

[Рабочее пространство Azure Machine Learning](concept-workspace.md) — это ресурс высшего уровня службы. Он предоставляет вам централизованное место для работы со всеми артефактами, которые вы создаете. В Python SDK можно получить доступ к артефактам рабочего пространства, создав [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) объект.

Создание объекта рабочей `config.json` области из файла, созданного в [разделе предпосылок.](#prerequisites)

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>Создание эксперимента в машинном обучении

Создайте эксперимент и папку для хранения обучаемых скриптов. В этом примере создайте эксперимент под названием "sklearn-iris".

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>Подготовка сценария обучения

В этом учебнике, учебный сценарий **train_iris.py** уже предусмотрено для вас. На практике вы должны иметь возможность принимать любой пользовательский сценарий обучения, как есть, и запускать его с Azure ML без необходимости изменять код.

Чтобы использовать возможности отслеживания и метрик Azure ML, добавьте небольшое количество кода Azure ML в свой обучающий скрипт.  Сценарий обучения **train_iris.py** показывает, как войти некоторые метрики в ваш Azure ML запустить с помощью `Run` объекта в скрипте.

В предоставленном обучаемом `iris = datasets.load_iris()` скрипте используются примеры данных из функции.  Для собственных данных может потребоваться использовать такие этапы, как [набор данных upload и скрипты,](how-to-train-keras.md#data-upload) чтобы сделать данные доступными во время обучения.

Копируйте сценарий обучения **train_iris.py** в каталог проекта.

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>Создание или создание вычислительной цели

Создайте цель вычислений для выполнения вашей работы по scikit-learn. Scikit-learn поддерживает только один узла, процессорные вычисления.

Следующий код создает управляемый вычислением Azure Machine Learning (AmlCompute) для удаленного учебного вычислительного ресурса. Создание AmlCompute занимает около 5 минут. Если AmlCompute с таким именем уже находится в вашем рабочем пространстве, этот код пропустит процесс создания.

```Python
cluster_name = "cpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Для получения дополнительной информации о [what is a compute target](concept-compute-target.md) целевых показателях вычислений см.

## <a name="create-a-scikit-learn-estimator"></a>Создание оценщика scikit-обучения

[Оценщик scikit-обучения](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) предоставляет простой способ запуска scikit-обучения обучение работу на вычислительной цели. Он реализуется [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) через класс, который может быть использован для поддержки одноузлового обучения процессора.

Если ваш учебный скрипт нуждается в дополнительных пакетов пипса или конды для `pip_packages` запуска, вы можете иметь пакеты, установленные на результирующем изображении докера, передавая их имена через `conda_packages` аргументы.

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py'
                    pip_packages=['joblib']
                   )
```


Для получения дополнительной информации о настройке среды Python [см.](how-to-use-environments.md) 

## <a name="submit-a-run"></a>Отправить пробег

[Объект Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) предоставляет интерфейс истории выполнения во время выполнения и после его завершения.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

По мере выполнения выполнения он проходит следующие этапы:

- **Подготовка**: Изображение докера создается в соответствии с оценкой TensorFlow. Изображение загружается в реестр контейнеров рабочего пространства и кэшируется для последующих запусков. Логи также передаются в историю выполнения и могут быть просмотрены для мониторинга прогресса.

- **Масштабирование**: Кластер пытается увеличить масштаб, если кластер ИИ пакетов требует больше узлов для выполнения выполнения, чем в настоящее время.

- **Запуск:** Все скрипты в папке скрипта загружаются в цель вычисления, хранилища данных монтируются или копируются, а entry_script выполняется. Выходы из stdout и папки ./logs передаются в историю выполнения и могут использоваться для мониторинга выполнения.

- **Постобработка**: Папка ./выходов запуска скопирована к истории выполнения.

## <a name="save-and-register-the-model"></a>Сохранить и зарегистрировать модель

После того как вы обучили модель, вы можете сохранить и зарегистрировать его в рабочее пространство. Регистрация моделей позволяет хранить и отстраивать модели в рабочем пространстве для упрощения [управления и развертывания моделей.](concept-model-management-and-deployment.md)

Добавьте следующий код в свой обучаемый скрипт, train_iris.py, чтобы сохранить модель. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Зарегистрируйте модель в рабочем пространстве со следующим кодом. При указании `model_framework` `model_framework_version`параметров, `resource_configuration`и развертывание модели без кода становится доступным. Это позволяет непосредственно развертывать модель в виде веб-сервиса [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py) из зарегистрированной модели, и объект определяет вычислительный ресурс для веб-сервиса.

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

Только что зарегистрированная модель может быть развернута точно так же, как и любая другая зарегистрированная модель в Azure Machine Learning, независимо от того, какой оценщик вы использовали для обучения. Способ развертывания содержит раздел о регистрации моделей, но можно перейти непосредственно к [созданию вычислительной цели](how-to-deploy-and-where.md#choose-a-compute-target) для развертывания, так как у вас уже есть зарегистрированная модель.

### <a name="preview-no-code-model-deployment"></a>(Предварительный просмотр) Развертывание модели без кода

Вместо традиционного маршрута развертывания можно также использовать функцию развертывания без кода (предварительный просмотр) для обучения scikit. Развертывание модели без кода поддерживается для всех встроенных типов scikit-learn. Зарегистрировав модель, как показано `model_framework` `model_framework_version`выше, `resource_configuration` с помощью , [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) и параметры, вы можете просто использовать статическую функцию для развертывания модели.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

ПРИМЕЧАНИЕ: Эти зависимости включены в заранее построенный scikit-узнать вывод контейнера.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

Полное [способное](how-to-deploy-and-where.md) развертывание в Azure Machine Learning более подробно.


## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы обучали и регистрировали модель scikit-learn и узнали о вариантах развертывания. Ознакомиться с другими статьями читайте в других статьях, чтобы узнать больше о машинном обучении Azure.

* [Отслеживание метрик выполнения во время обучения](how-to-track-experiments.md)
* [Настройка гиперпараметров](how-to-tune-hyperparameters.md)
* [Справочная архитектура для распределенного глубокого обучения в Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
