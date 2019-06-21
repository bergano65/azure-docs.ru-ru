---
title: Обучение и зарегистрировать PyTorch моделей
titleSuffix: Azure Machine Learning service
description: В этой статье показано, как обучать и зарегистрировать PyTorch модели с помощью службы машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 06/18/2019
ms.custom: seodec18
ms.openlocfilehash: fc80fcde8de3fb2d6dd6f59804f6019b76aa8727
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295599"
---
# <a name="train-and-register-pytorch-models-at-scale-with-azure-machine-learning-service"></a>Обучение и зарегистрировать PyTorch модели в масштабе с помощью службы машинного обучения Azure

В этой статье показано, как обучать и зарегистрировать PyTorch модели с помощью службы машинного обучения Azure. Он основан на [переноса PyTorch обучения руководстве](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html) которое создает классификатора глубокой нейронной сети (DNN) для образов ants и пчел.

[PyTorch](https://pytorch.org/) — это платформа вычислений открытым исходным кодом, обычно используется для создания глубоких нейронных сетей (DNN). С помощью службы машинного обучения Azure вы можете быстро выполнить развертывание заданий обучения с открытым исходным кодом, с помощью эластичных облачных вычислительных ресурсов. Вы также можете отслеживать свои учебных запусков, моделях версии развертывание моделей и многое другое.

При разработке модели PyTorch из нуля, или вам нужно добавить существующую модель в облаке, службы машинного обучения Azure поможет вам создавать готовые модели.

## <a name="prerequisites"></a>Технические условия

Выполните этот код на любой из этих сред:

 - Machine Learning записные книжки Azure виртуальной Машины — нет файлы для загрузки или установки не требуется

    - Завершить [быстрого запуска облачной записной книжки](quickstart-run-cloud-notebook.md) создание специальных ноутбуков сервера предварительно загруженным с помощью пакета SDK и репозиторий с примером.
    - В папке samples на сервере записной книжки, найти записную книжку выполненной и развернутое, перейдя к этому каталогу: **практические-в-использование azureml > обучения с помощью глубокого обучения > train-hyperparameter-tune-deploy-with-pytorch** папка. 
 
 - Собственный сервер Jupyter Notebook

    - [Установка Azure Machine Learning пакета SDK для Python](setup-create-workspace.md#sdk)
    - [Создайте файл конфигурации рабочей области](setup-create-workspace.md#write-a-configuration-file)
    - [Скачайте файлы образца сценария](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch) `pytorch_train.py`
     
    Вы также найдете завершенного [версии записной книжки Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) данного руководства на странице образцов GitHub. Записная книжка включает расширенный, освещая интеллектуальной настройки гиперпараметров, развертывание модели и записной книжки мини-приложения.

## <a name="set-up-the-experiment"></a>Настроить эксперимент

В этом разделе устанавливает обучающий эксперимент с помощью загрузки пакетов требуется python, инициализация рабочей области, создания эксперимента и отправки данных для обучения и скриптов обучения.

### <a name="import-packages"></a>Импорт пакетов

Во-первых импортируйте необходимые библиотеки Python.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import PyTorch
```

### <a name="initialize-a-workspace"></a>Инициализация рабочей области

[Рабочей области машинного обучения Azure службы](concept-workspace.md) — это ресурс верхнего уровня для службы. Он предоставляет централизованное расположение для работы с все артефакты, созданные. В пакете SDK для Python, можно получить доступ к артефактам рабочей области путем создания [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) объекта.

Создать объект рабочей области из `config.json` файла, созданного в [предварительных](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Создание эксперимента

Создание эксперимента и папку для хранения сценариев обучения. В этом примере Создайте эксперимент, который называется «pytorch hymenoptera».

```Python
project_folder = './pytorch-hymenoptera'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-hymenoptera'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Получение данных

Набор данных состоит из около 120 обучающие изображения каждого ants и пчел, 75 проверки образов для каждого класса. Hymenoptera приведен порядок насекомых, включающий ants и пчел. Загрузите и извлеките набор данных как часть нашего сценария обучения `pytorch_train.py`.

### <a name="prepare-training-scripts"></a>Подготовка скриптов обучения

В этом руководстве скрипт обучения, `pytorch_train.py`, по умолчанию в нее. На практике можно воспользоваться любой сценарий курс, как и запустите его с помощью службы машинного обучения Azure.

Отправить скрипт обучения Pytorch, `pytorch_train.py`.

```Python
shutil.copy('pytorch_train.py', project_folder)
```

Тем не менее если вы хотите использовать метрики возможности отслеживания службы машинного обучения Azure и, необходимо добавить небольшой код внутри сценария обучения. Примеры отслеживания метрик можно найти в `pytorch_train.py`.

## <a name="create-a-compute-target"></a>Создание целевого объекта вычислений

Создание целевого объекта вычислений для задания PyTorch под управлением. В этом примере создания вычислительного кластера с поддержкой GPU машинного обучения Azure.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Дополнительные сведения о целевых объектах вычислений см. в разделе [Какова целевого объекта вычислений](concept-compute-target.md) статьи.

## <a name="create-a-pytorch-estimator"></a>Создание оценки PyTorch

[PyTorch estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) предоставляет простой способ запуска задание обучения PyTorch на целевой объект вычислений.

Оценщик PyTorch реализуется с помощью универсального [ `estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) класс, который может использоваться для поддержки любой платформы. Дополнительные сведения об обучении моделей с помощью универсальный механизм оценки, см. в разделе [обучения моделей с машинным обучением Azure, с помощью механизма оценки](how-to-train-ml-models.md)

Если сценарий обучения требуются дополнительные pip или conda пакеты для запуска, может иметь пакетами, устанавливаемыми на итоговый образ docker, передав их имена через `pip_packages` и `conda_packages` аргументы.

```Python
script_params = {
    '--num_epochs': 30,
    '--output_dir': './outputs'
}

estimator = PyTorch(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='pytorch_train.py',
                    use_gpu=True,
                    pip_packages=['pillow==5.4.1'])
```

## <a name="submit-a-run"></a>Запуске

[Выполнения объекта](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) предоставляет интерфейс для журнала выполнения, пока выполняется задание, и после его завершения.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Как выполняется запуска, он проходит через следующие этапы:

- **Подготовка**: В соответствии с оценщика PyTorch создается образ docker. Изображение отправлены в реестр контейнеров в рабочей области и кэшируются для выполнения более поздней версии. Также будут отправлены в журнал выполнения и отслеживать ход выполнения можно просмотреть журналы.

- **Масштабирование**. Кластера пытается увеличить масштаб, если для кластера Batch AI требуется больше узлов для выполнения запуска, чем в настоящее время доступны.

- **Running**. Все сценарии в папке сценария передаются целевого объекта вычислений, хранилищ данных отправляются подключен или копируются, а также выполняется entry_script. Выходные данные stdout и. / Папка журналов будут отправлены в журнал выполнения и может использоваться для отслеживания выполнения.

- **Постобработка**. . / Outputs папки выполнения, копируются в журнал выполнения.

## <a name="register-or-download-a-model"></a>Зарегистрировать или загрузить модель

После обучения модели, можно зарегистрировать его в рабочую область. Регистрация модели позволяет хранилища и версию модели в рабочей области для упрощения [моделей управления и развертывания](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

Также можно загрузить локальную копию модели с помощью объекта выполнения. В сценарий обучения `pytorch_train.py`, PyTorch сохранить объект сохраняет модель в локальную папку (локальным для целевого объекта вычислений). Объект выполнения можно использовать для загрузки копии.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Распределенное обучение

[ `PyTorch` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) Estimator также поддерживает распределенного обучения по кластерам ЦП и GPU. Можно легко выполнять распределенные задания PyTorch и службы машинного обучения Azure будет администрировать оркестрацию для вас.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) является открытым кодом, для распределенного обучения разработанный Uber понизить framework. Он предлагает простой способ распределенные задания GPU PyTorch.

Чтобы использовать Horovod, укажите [ `MpiConfiguration` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) для объекта `distributed_training` параметра конструктору объекта PyTorch. Этот параметр гарантирует, что библиотека Horovod устанавливается для использования в скрипте обучения.


```Python
from azureml.train.dnn import PyTorch

estimator= PyTorch(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```
Horovod и его зависимости будут установлены для, поэтому его можно импортировать в скрипте обучения `train.py` следующим образом:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Экспорт в ONNX

Для оптимизации вывод с [среды выполнения ONNX](concept-onnx.md), преобразуются в формат ONNX обученной модели PyTorch. Определение или модель оценки, — это этап, где используется развернутой модели для прогнозирования, чаще всего для производственных данных. См. в разделе [руководстве](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) пример.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы обучена и зарегистрировали модели PyTorch службе машинного обучения Azure. Чтобы узнать, как развернуть модель, перейдите к статье развертывания нашей модели.

> [!div class="nextstepaction"]
> [Как и способа развертывания модели](how-to-deploy-and-where.md)
* [Отслеживание метрик выполнения во время обучения](how-to-track-experiments.md)
* [Настройка гиперпараметров](how-to-tune-hyperparameters.md)
* [Развертывание обученной модели](how-to-deploy-and-where.md)
