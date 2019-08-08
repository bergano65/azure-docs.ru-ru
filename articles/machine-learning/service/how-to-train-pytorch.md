---
title: Обучение нейронной сети для глубокого обучения с помощью PyTorch
titleSuffix: Azure Machine Learning service
description: Узнайте, как выполнять сценарии обучения PyTorch в масштабе предприятия с помощью класса оценщика PyTorch Машинное обучение Azure.  Примеры сценариев классифицируют Chicken и Турция изображений для создания нейронной сети глубокого обучения на основе руководства по переносу PyTorch.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 2fe3e222da265614ff64e3d913f6514398abf32b
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847671"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>Обучить модели глубокого обучения Pytorch в масштабе с помощью Машинное обучение Azure

Из этой статьи вы узнаете, как выполнять сценарии обучения [PyTorch](https://pytorch.org/) в масштабе предприятия с помощью класса [оценщика PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) машинное обучение Azure.  

Примеры сценариев в этой статье используются для классификации образов Chicken и Турция для создания нейронной сети глубокого обучения на основе [руководства](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)по переносу PyTorch. 

Независимо от того, где вы изучаете модель PyTorch глубокого обучения или используете существующую модель в облаке, вы можете использовать Машинное обучение Azure для масштабирования заданий обучения с открытым исходным кодом с помощью эластичных облачных ресурсов. Вы можете создавать, развертывать, выполнять версии и отслеживать модели производственного уровня с помощью Машинное обучение Azure. 

Дополнительные сведения о [глубоком обучении и машинном](concept-deep-learning-vs-machine-learning.md)обучении.

## <a name="prerequisites"></a>предварительные требования

Запустите этот код в любой из этих сред:

 - Машинное обучение Azure виртуальной машины записной книжки — Загрузка или установка не требуется

    - Пройдите [руководство по Настройте среду и рабочую](tutorial-1st-experiment-sdk-setup.md) область, чтобы создать выделенный сервер записной книжки, предварительно загруженный с помощью пакета SDK и примера репозитория.
    - В папке примеры глубокого обучения на сервере записной книжки найдите готовую и развернутую записную книжку, перейдя к этому каталогу: **практические советы по использованию azureml > Обучение с помощью-глубоко-learning > Обучение-параметр-Настройка-Deploy-with-pytorch** . 
 
 - Собственный сервер Jupyter Notebook

    - [Установите пакет SDK для машинное обучение Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Создайте файл конфигурации рабочей области](how-to-configure-environment.md#workspace).
    - [Загрузка примеров файлов скриптов](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    Вы также можете найти завершенную [Jupyter Notebook версию](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) этого руководства на странице примеров GitHub. Записная книжка включает в себя развернутые разделы, охватывающие интеллектуальные настройки, развертывание моделей и мини-приложения записных книжек.

## <a name="set-up-the-experiment"></a>Настройка эксперимента

В этом разделе выполняется настройка обучающего эксперимента путем загрузки требуемых пакетов Python, инициализации рабочей области, создания эксперимента и отправки обучающих данных и сценариев обучения.

### <a name="import-packages"></a>Импорт пакетов

Сначала импортируйте необходимые библиотеки Python.

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

[Рабочая область службы машинное обучение Azure](concept-workspace.md) — это ресурс верхнего уровня для службы. Он обеспечивает централизованное расположение для работы со всеми создаваемыми артефактами. В пакете SDK для Python можно получить доступ к артефактам рабочей области, [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) создав объект.

Создайте объект рабочей области из `config.json` файла, созданного в [разделе Предварительные требования](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Создание эксперимента глубокого обучения

Создайте эксперимент и папку для хранения сценариев обучения. В этом примере Создайте эксперимент с именем «pytorch-птиц».

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Получение данных

Набор данных состоит из примерно 120 учебных изображений для туркэйс и чиккенс с образами проверки 100 для каждого класса. Мы будем скачивать и извлекать набор данных в рамках нашего обучающего `pytorch_train.py`сценария. Изображения являются подмножеством [набора данных Open Images V5](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-scripts"></a>Подготовка сценариев обучения

В этом руководстве обучающий сценарий `pytorch_train.py`уже предоставлен. На практике вы можете использовать любой пользовательский сценарий обучения, как есть, и запустить его со службой Машинное обучение Azure.

Отправьте сценарий обучения Pytorch, `pytorch_train.py`.

```Python
shutil.copy('pytorch_train.py', project_folder)
```

Тем не менее, если вы хотите использовать функции отслеживания служб Машинное обучение Azure и метрики, необходимо добавить небольшой код объема в обучающий сценарий. Примеры отслеживания метрик можно найти в `pytorch_train.py`.

## <a name="create-a-compute-target"></a>Создание целевого объекта вычислений

Создайте целевой объект вычислений для выполнения задания PyTorch. В этом примере создайте кластерный Машинное обучение Azure вычислений с поддержкой GPU.

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

Дополнительные сведения о целевых объектах вычислений см. в статье [что такое целевые показатели вычислений](concept-compute-target.md) .

## <a name="create-a-pytorch-estimator"></a>Создание средства оценки PyTorch

Средство [оценки PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) предоставляет простой способ запуска задания обучения PyTorch на целевом объекте вычислений.

Оценщик PyTorch реализуется через универсальный [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) класс, который можно использовать для поддержки любой платформы. Дополнительные сведения о учебных моделях с помощью универсального средства оценки см. в статье [обучение моделей с машинное обучение Azure помощью средства оценки](how-to-train-ml-models.md) .

Если для выполнения сценария обучения требуются дополнительные пакеты PIP или conda, можно установить пакеты в полученном образе DOCKER, передав их имена с помощью `pip_packages` аргументов и. `conda_packages`

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

## <a name="submit-a-run"></a>Отправка запуска

[Объект Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) предоставляет интерфейс для журнала выполнения во время выполнения задания и после его завершения.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

При выполнении выполнения он проходит следующие этапы.

- **Идет подготовка**: Образ DOCKER создается в соответствии с оценкой PyTorch. Образ отправляется в реестр контейнеров рабочей области и кэшируется для последующего выполнения. Журналы также передаются в журнал выполнения и могут быть просмотрены для отслеживания хода выполнения.

- **Масштабирование**. Кластер пытается выполнить масштабирование, если кластеру Batch AI требуется больше узлов для выполнения выполнения, чем доступно в данный момент.

- **Running**. Все скрипты в папке скрипта передаются в целевой объект вычислений, хранилища данных подключаются или копируются, и выполняется entry_script. Выходные данные из STDOUT и папки/логс передаются в журнал выполнения и могут использоваться для наблюдения за выполнением.

- **Постобработка**. Папка./Outputs выполнения копируется в журнал выполнения.

## <a name="register-or-download-a-model"></a>Регистрация или скачивание модели

После обучения модели ее можно зарегистрировать в рабочей области. Регистрация модели позволяет хранить и отменять версии моделей в рабочей области, чтобы упростить [Управление моделями и их развертывание](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

Вы также можете скачать локальную копию модели с помощью объекта Run. В обучающем скрипте `pytorch_train.py`объект PyTorch Save сохраняет модель в локальную папку (локальную для целевого объекта вычислений). Для скачивания копии можно использовать объект Run.

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

[`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) Оценщик также поддерживает распределенное обучение между кластерами ЦП и GPU. Вы можете легко выполнять распределенные задания PyTorch, а Машинное обучение Azure служба будет управлять согласованием.

### <a name="horovod"></a>Horovod
[Хоровод](https://github.com/uber/horovod) — это платформа с открытым исходным кодом, которая сокращает структуру для распределенного обучения, разработанного Uber. Он предоставляет простой путь к распределенным заданиям PyTorch GPU.

Чтобы использовать хоровод, укажите [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) объект `distributed_training` для параметра в конструкторе PyTorch. Этот параметр обеспечивает установку библиотеки хоровод для использования в обучающем скрипте.


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
Хоровод и его зависимости будут установлены для вас, поэтому его можно импортировать в сценарий `train.py` обучения следующим образом:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Экспорт в ONNX

Чтобы оптимизировать вывод с помощью [среды выполнения ONNX](concept-onnx.md), преобразуйте обученную модель PyTorch в формат ONNX. Вывод или оценка модели — это этап, в котором развернутая модель используется для прогнозирования, чаще всего в рабочих данных. Пример см. в [руководстве](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) .

## <a name="next-steps"></a>Следующие шаги

В этой статье вы обучили и зарегистрировали нейронную сеть глубокого обучения с помощью PyTorch в службе Машинное обучение Azure. Чтобы узнать, как развернуть модель, перейдите к статье о развертывании модели.

> [!div class="nextstepaction"]
> [Как и где развертываются модели](how-to-deploy-and-where.md)
* [Отслеживание метрик выполнения во время обучения](how-to-track-experiments.md)
* [Настройка гиперпараметров](how-to-tune-hyperparameters.md)
* [Развертывание обученной модели](how-to-deploy-and-where.md)
