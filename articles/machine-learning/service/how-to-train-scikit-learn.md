---
title: Обучение моделей машинного обучения с помощью scikit — обучение
titleSuffix: Azure Machine Learning service
description: Узнайте, как выполнять сценарии обучения scikit-учиться в масштабе предприятия с помощью класса оценщика SKlearn Машинное обучение Azure. Примеры сценариев классифицируют изображения диафрагмы для создания модели машинного обучения на основе набора данных диафрагмы scikit-Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/02/2019
ms.custom: seodec18
ms.openlocfilehash: e76a747f7e1d7ca7056edf5b69df0677aeff96a1
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856001"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning-service"></a>Создание scikit. изучение моделей в масштабе с помощью службы Машинное обучение Azure

Из этой статьи вы узнаете, как выполнять сценарии обучения scikit-учиться в масштабе предприятия с помощью класса средства [оценки SKlearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) для машинное обучение Azure. 

Примеры сценариев, приведенные в этой статье, используются для классификации изображений диафрагмы для создания модели машинного обучения на основе [набора данных диафрагмы](https://archive.ics.uci.edu/ml/datasets/iris)scikit.

Если вы научитесь изучать модель машинного обучения scikit-учиться с нуля или используете существующую модель в облаке, вы можете использовать Машинное обучение Azure для масштабирования заданий обучения с открытым исходным кодом с помощью эластичных облачных ресурсов. Вы можете создавать, развертывать, разворачивать и отслеживать модели производственного уровня с помощью Машинное обучение Azure.

## <a name="prerequisites"></a>предварительные требования

Запустите этот код в любой из этих сред:
 - Машинное обучение Azure виртуальной машины записной книжки — Загрузка или установка не требуется

    - Пройдите [руководство по Настройте среду и рабочую](tutorial-1st-experiment-sdk-setup.md) область, чтобы создать выделенный сервер записной книжки, предварительно загруженный с помощью пакета SDK и примера репозитория.
    - В папке обучающие примеры на сервере записной книжки найдите готовую и развернутую записную книжку, перейдя к этому каталогу: **практические советы по использованию azureml > обучения > Train-i Parameter-Настройка-Deploy-with-sklearn** Folder.

 - Собственный сервер Jupyter Notebook

    - [Установите пакет SDK для машинное обучение Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Создайте файл конфигурации рабочей области](how-to-configure-environment.md#workspace).
    - Скачайте набор данных и пример файла скрипта 
        - [набор данных IRI](https://archive.ics.uci.edu/ml/datasets/iris)
        - [`train_iris.py`](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn)
    - Вы также можете найти завершенную [Jupyter Notebook версию](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) этого руководства на странице примеров GitHub. Записная книжка включает в себя развернутую секцию, охватывающую настройку интеллектуальной настройки, и получение лучшей модели по основным метрикам.

## <a name="set-up-the-experiment"></a>Настройка эксперимента

В этом разделе выполняется настройка обучающего эксперимента путем загрузки требуемых пакетов Python, инициализации рабочей области, создания эксперимента и отправки обучающих данных и сценариев обучения.

### <a name="import-packages"></a>Импорт пакетов

Сначала импортируйте необходимые библиотеки Python.

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

### <a name="initialize-a-workspace"></a>Инициализация рабочей области

[Рабочая область службы машинное обучение Azure](concept-workspace.md) — это ресурс верхнего уровня для службы. Он обеспечивает централизованное расположение для работы со всеми создаваемыми артефактами. В пакете SDK для Python можно получить доступ к артефактам рабочей области, [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) создав объект.

Создайте объект рабочей области из `config.json` файла, созданного в [разделе Предварительные требования](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>Создание эксперимента машинного обучения

Создайте эксперимент и папку для хранения сценариев обучения. В этом примере Создайте эксперимент с названием «sklearn-IRI».

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="upload-dataset-and-scripts"></a>Отправка набора данных и скриптов

[Хранилище](how-to-access-data.md) данных — это место для хранения и доступа к данным путем подключения или копирования данных в целевой объект вычислений. Каждая Рабочая область предоставляет хранилище данных по умолчанию. Передайте данные и обучающие скрипты в хранилище данных, чтобы к ним можно было легко получить доступ во время обучения.

1. Создайте каталог для своих данных.

    ```Python
    os.makedirs('./data/iris', exist_ok=True)
    ```

1. Передайте набор данных IRI в хранилище, заданное по умолчанию.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/iris', target_path='iris', overwrite=True, show_progress=True)
    ```

1. Отправьте сценарий обучения scikit-учиться, `train_iris.py`.

    ```Python
    shutil.copy('./train_iris.py', project_folder)
    ```

## <a name="create-or-get-a-compute-target"></a>Создание или получение целевого объекта вычислений

Создайте целевой объект вычислений для выполнения задания scikit-учиться. Scikit — сведения поддерживают только один узел, вычислительные ресурсы ЦП.

Следующий код создает Машинное обучение Azure управляемого вычислений (Амлкомпуте) для удаленного обучающего ресурса. Создание Амлкомпуте занимает примерно 5 минут. Если Амлкомпуте с таким именем уже находится в рабочей области, этот код пропустит процесс создания.

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

Дополнительные сведения о целевых объектах вычислений см. в статье [что такое целевые показатели вычислений](concept-compute-target.md) .

## <a name="create-a-scikit-learn-estimator"></a>Создание средства оценки scikit-учиться

Средство [оценки scikit-](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) Training предоставляет простой способ запуска задания обучения scikit-учиться на целевом объекте вычислений. Он реализуется с помощью [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) класса, который можно использовать для поддержки обучения ЦП с одним узлом.

Если для выполнения сценария обучения требуются дополнительные пакеты PIP или conda, можно установить пакеты в полученном образе DOCKER, передав их имена с помощью `pip_packages` аргументов и. `conda_packages`

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

## <a name="submit-a-run"></a>Отправка запуска

[Объект Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) предоставляет интерфейс для журнала выполнения во время выполнения задания и после его завершения.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

При выполнении выполнения он проходит следующие этапы.

- **Идет подготовка**: Образ DOCKER создается в соответствии с оценкой TensorFlow. Образ отправляется в реестр контейнеров рабочей области и кэшируется для последующего выполнения. Журналы также передаются в журнал выполнения и могут быть просмотрены для отслеживания хода выполнения.

- **Масштабирование**. Кластер пытается выполнить масштабирование, если кластеру Batch AI требуется больше узлов для выполнения выполнения, чем доступно в данный момент.

- **Running**. Все скрипты в папке скрипта передаются в целевой объект вычислений, хранилища данных подключаются или копируются, и выполняется entry_script. Выходные данные из STDOUT и папки/логс передаются в журнал выполнения и могут использоваться для наблюдения за выполнением.

- **Постобработка**. Папка./Outputs выполнения копируется в журнал выполнения.

## <a name="save-and-register-the-model"></a>Сохранение и регистрация модели

После обучения модели ее можно сохранить и зарегистрировать в рабочей области. Регистрация модели позволяет хранить и отменять версии моделей в рабочей области, чтобы упростить [Управление моделями и их развертывание](concept-model-management-and-deployment.md).

Добавьте следующий код в сценарий обучения train_iris. корректировки, чтобы сохранить модель. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Зарегистрируйте модель в рабочей области с помощью следующего кода.

```Python
model = run.register_model(model_name='sklearn-iris', model_path='model.joblib')
```

## <a name="next-steps"></a>Следующие шаги

В этой статье вы обучили и зарегистрировали машинное обучение, модель классификации с помощью scikit-Learning on Машинное обучение Azure Service.

* Чтобы узнать, как развернуть модель, перейдите к статье о [развертывании модели](how-to-deploy-and-where.md) .

* [Настройка параметров](how-to-tune-hyperparameters.md).

* [Следите за показателями запуска во время обучения](how-to-track-experiments.md).

* Дополнительные сведения о [глубоком обучении и машинном](concept-deep-learning-vs-machine-learning.md)обучении.