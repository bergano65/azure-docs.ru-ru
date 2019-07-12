---
title: Обучение и зарегистрировать scikit-Узнайте моделей
titleSuffix: Azure Machine Learning service
description: В этой статье показано, как обучать и зарегистрировать scikit-дополнительные модели с помощью службы машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 06/30/2019
ms.custom: seodec18
ms.openlocfilehash: c9e983f7981c1155964617694d2cce86aba741b7
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840024"
---
# <a name="train-and-register-scikit-learn-models-at-scale-with-azure-machine-learning-service"></a>Обучение и зарегистрировать Scikit-learn модели в масштабе с помощью службы машинного обучения Azure

В этой статье показано, как обучать и зарегистрируйте модели Scikit-learn, с помощью службы машинного обучения Azure. Она использует популярного [набор данных Iris](https://archive.ics.uci.edu/ml/datasets/iris) для классификации изображений цветок iris с помощью пользовательского [scikit-Узнайте](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) класса.

Scikit-learn – это инфраструктура вычислений открытым исходным кодом, обычно используется для машинного обучения. С помощью службы машинного обучения Azure вы можете быстро выполнить развертывание заданий обучения с открытым исходным кодом, с помощью эластичных облачных вычислительных ресурсов. Вы также можете отслеживать свои учебных запусков, моделях версии развертывание моделей и многое другое.

При разработке модели Scikit-learn из нуля, или вам нужно добавить существующую модель в облаке, службы машинного обучения Azure поможет вам создавать готовые модели.

## <a name="prerequisites"></a>Предварительные требования

Выполните этот код на любой из этих сред:
 - Machine Learning записные книжки Azure виртуальной Машины — нет файлы для загрузки или установки не требуется

    - Завершить [быстрого запуска облачной записной книжки](quickstart-run-cloud-notebook.md) создание специальных ноутбуков сервера предварительно загруженным с помощью пакета SDK и репозиторий с примером.
    - В папке samples на сервере записной книжки, найти записную книжку выполненной и развернутое, перейдя к этому каталогу: **практические-в-использование azureml > Обучение > train-hyperparameter-tune-deploy-with-sklearn** папки.

 - Собственный сервер Jupyter Notebook

    - [Установка Azure Machine Learning пакета SDK для Python](setup-create-workspace.md#sdk)
    - [Создайте файл конфигурации рабочей области](setup-create-workspace.md#write-a-configuration-file)
    - [Скачать пример файла скрипта](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn) `train_iris.py`
    - Вы также найдете завершенного [версии записной книжки Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-sklearn.ipynb) данного руководства на странице образцов GitHub. Записная книжка включает расширенный раздел, охватывающий intelligent гиперпараметров, настройке и получение оптимальную модель с первичных показателей.

## <a name="set-up-the-experiment"></a>Настроить эксперимент

В этом разделе устанавливает обучающий эксперимент с помощью загрузки пакетов требуется python, инициализация рабочей области, создания эксперимента и отправки данных для обучения и скриптов обучения.

### <a name="import-packages"></a>Импорт пакетов

Во-первых импортируйте необходимые библиотеки Python.

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

[Рабочей области машинного обучения Azure службы](concept-workspace.md) — это ресурс верхнего уровня для службы. Он предоставляет централизованное расположение для работы с все артефакты, созданные. В пакете SDK для Python, можно получить доступ к артефактам рабочей области путем создания [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) объекта.

Создать объект рабочей области из `config.json` файла, созданного в [предварительных](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Создание эксперимента

Создание эксперимента и папку для хранения сценариев обучения. В этом примере Создайте эксперимент, который называется «sklearn-цветков ириса».

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="upload-dataset-and-scripts"></a>Передача набора данных и сценариев

[Хранилище данных](how-to-access-data.md) — это место, где можно хранить данные и осуществляется подключаются или при копировании данных на целевой объект вычислений. Каждая рабочая область предоставляет хранилище данных по умолчанию. Передача данных и скриптов обучения в хранилище данных, чтобы они могут быть легко доступны во время обучения.

1. Создайте каталог для ваших данных.

    ```Python
    os.makedirs('./data/iris', exist_ok=True)
    ```

1. Отправьте набор данных iris в хранилище данных по умолчанию.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/iris', target_path='iris', overwrite=True, show_progress=True)
    ```

1. Скрипт обучения отправки, Scikit-learn, `train_iris.py`.

    ```Python
    shutil.copy('./train_iris.py', project_folder)
    ```

## <a name="create-or-get-a-compute-target"></a>Создать или получить целевой объект вычислений

Создание целевого объекта вычислений для задания Scikit-learn под управлением. Scikit Узнайте только поддерживает один узел, вычисление ЦП.

Следующий код создает управляемые машинного обучения Azure compute (AmlCompute) для ресурса вычислений удаленное обучение. Создание из AmlCompute занимает примерно 5 минут. Если AmlCompute с таким именем уже существует в рабочей области, этот код будет пропускать в процессе создания.

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

Дополнительные сведения о целевых объектах вычислений см. в разделе [Какова целевого объекта вычислений](concept-compute-target.md) статьи.

## <a name="create-a-scikit-learn-estimator"></a>Создание оценки, Scikit-learn

[Scikit-learn оценщика](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) предоставляет простой способ запуска, Scikit-learn задания обучения на целевой объект вычислений. Он реализуется с помощью [ `SKLearn` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) класс, который может использоваться для поддержки обучения ЦП одного узла.

Если сценарий обучения требуются дополнительные pip или conda пакеты для запуска, может иметь пакетами, устанавливаемыми на итоговый образ docker, передав их имена через `pip_packages` и `conda_packages` аргументы.

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

## <a name="submit-a-run"></a>Запуске

[Выполнения объекта](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) предоставляет интерфейс для журнала выполнения, пока выполняется задание, и после его завершения.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Как выполняется запуска, он проходит через следующие этапы:

- **Подготовка**: В соответствии с оценщика TensorFlow создается образ docker. Изображение отправлены в реестр контейнеров в рабочей области и кэшируются для выполнения более поздней версии. Также будут отправлены в журнал выполнения и отслеживать ход выполнения можно просмотреть журналы.

- **Масштабирование**. Кластера пытается увеличить масштаб, если для кластера Batch AI требуется больше узлов для выполнения запуска, чем в настоящее время доступны.

- **Running**. Все сценарии в папке сценария передаются целевого объекта вычислений, хранилищ данных отправляются подключен или копируются, а также выполняется entry_script. Выходные данные stdout и. / Папка журналов будут отправлены в журнал выполнения и может использоваться для отслеживания выполнения.

- **Постобработка**. . / Outputs папки выполнения, копируются в журнал выполнения.

## <a name="save-and-register-the-model"></a>Сохранение и регистрация модели

После обучения модели, можно сохранить и зарегистрировать его в рабочую область. Регистрация модели позволяет хранилища и версию модели в рабочей области для упрощения [моделей управления и развертывания](concept-model-management-and-deployment.md).

Добавьте приведенный ниже сценарий обучения, train_iris.py, чтобы сохранить модель. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Зарегистрируйте модель в рабочую область на приведенный ниже.

```Python
model = run.register_model(model_name='sklearn-iris', model_path='model.joblib')
```

## <a name="next-steps"></a>Следующие шаги

В этой статье вы обучена и зарегистрировали Scikit-learn модели службе машинного обучения Azure.

* Чтобы узнать, как развернуть модель, перейдите к нашей [развертывание модели](how-to-deploy-and-where.md) статьи.

* [Настройка гиперпараметров](how-to-tune-hyperparameters.md)

* [Отслеживание метрик выполнения во время обучения](how-to-track-experiments.md)