---
title: Поезд глубокого обучения PyTorch моделей
titleSuffix: Azure Machine Learning
description: Узнайте, как запускать скрипты обучения PyTorch в корпоративном масштабе с помощью класса оценки PyTorch компании Azure Machine Learning.  Пример скриптов классифицировать курица и индейка изображения для создания глубокого обучения нейронной сети на основе PyTorch передачи обучения учебник.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 136ee197271fc659497c169e27a6399c3940c19e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834861"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>Поезд Pytorch глубокого обучения модели в масштабе с Azure машинного обучения
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье узнайте, как запускать скрипты обучения [PyTorch](https://pytorch.org/) в корпоративном масштабе с помощью класса [оценщиков PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) компании Azure Machine Learning.  

Пример скриптов в этой статье используются для классификации изображений курицы и индейки, чтобы построить глубокую нейронную сеть обучения на основе [учебника](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)обучения передачи PyTorch. 

Независимо от того, обучаете ли вы модель PyTorch глубокому обучению с нуля или приносите существующую модель в облако, вы можете использовать Azure Machine Learning для масштабирования обучающих заданий с открытым исходным кодом с использованием эластичных облачных вычислительных ресурсов. Можно создавать, развертывать, развертывать и контролировать производственные модели с помощью Машинного обучения Azure. 

Узнайте больше о [глубоком обучении и машинном обучении.](concept-deep-learning-vs-machine-learning.md)

## <a name="prerequisites"></a>Предварительные требования

Выполнить этот код на любой из этих сред:

- Экземпляр вычислений машинного обучения Azure - нет необходимости загружать или установку

    - Завершите [обучение: Настройка среды и рабочего пространства](tutorial-1st-experiment-sdk-setup.md) для создания специального ноутбука сервера предварительно загружены с SDK и образец репозитория.
    - В образцах глубокого обучения папку на ноутбуке сервера, найти завершенный и расширенный ноутбук, перемещаясь в этот каталог: **как-к-использованию-azureml > обучение-с-глубокого обучения > поезд-гиперпараметр-настройка-развертывание-с-питорка** папку. 
 
 - Собственный сервер Jupyter Notebook

    - [Установите SDK машинного обучения Azure.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
    - [Создание файла конфигурации рабочего пространства.](how-to-configure-environment.md#workspace)
    - [Скачать пример файлы сценария](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    Вы также можете найти завершенную [версию Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) этого руководства на странице образцов GitHub. Ноутбук включает в себя расширенные разделы, охватывающие интеллектуальную настройку гиперпараментов, развертывание моделей и виджеты ноутбуков.

## <a name="set-up-the-experiment"></a>Настройка эксперимента

В этом разделе устанавливается учебный эксперимент путем загрузки необходимых пакетов питона, инициализации рабочего пространства, создания эксперимента и загрузки обучающих данных и обучающих скриптов.

### <a name="import-packages"></a>Импорт пакетов

Во-первых, импортируйте необходимые библиотеки Python.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import PyTorch
```

### <a name="initialize-a-workspace"></a>Инициализация рабочего пространства

[Рабочее пространство Azure Machine Learning](concept-workspace.md) — это ресурс высшего уровня службы. Он предоставляет вам централизованное место для работы со всеми артефактами, которые вы создаете. В Python SDK можно получить доступ к артефактам рабочего пространства, создав [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) объект.

Создание объекта рабочей `config.json` области из файла, созданного в [разделе предпосылок.](#prerequisites)

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Создание эксперимента по глубокому обучению

Создайте эксперимент и папку для хранения обучаемых скриптов. В этом примере создайте эксперимент под названием «питорч-птицы».

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Получение данных

Набор данных состоит из около 120 обучающие изображения для индеек и кур, с 100 изображениями проверки для каждого класса. Мы загрузим и извлекем набор данных как часть нашего сценария `pytorch_train.py`обучения. Изображения представляют собой подмножество [набора данных Open Images v5.](https://storage.googleapis.com/openimages/web/index.html)

### <a name="prepare-training-scripts"></a>Подготовка сценариев обучения

В этом учебнике, `pytorch_train.py`учебный сценарий, , уже предусмотрено. На практике можно взять любой пользовательский сценарий обучения, как есть, и запустить его с Помощью Azure Machine Learning.

Загрузите сценарий `pytorch_train.py`обучения Pytorch, .

```Python
shutil.copy('pytorch_train.py', project_folder)
```

Однако, если вы хотите использовать возможности отслеживания и метрик Azure Machine Learning, вам придется добавить небольшое количество кода в свой учебный скрипт. Примеры отслеживания метрик `pytorch_train.py`можно найти в .

## <a name="create-a-compute-target"></a>Создание целевого объекта вычислений

Создайте цель вычислений для выполнения задания PyTorch. В этом примере создайте кластер вычислений с поддержкой графического процессора Azure Machine Learning.

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

Для получения дополнительной информации о [what is a compute target](concept-compute-target.md) целевых показателях вычислений см.

## <a name="create-a-pytorch-estimator"></a>Создание оценщика PyTorch

[Оценщик PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) предоставляет простой способ запуска pyTorch учебной работы на вычислительной цели.

Оценщик PyTorch реализуется [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) через общий класс, который может быть использован для поддержки любых рамок. Для получения дополнительной информации о обучающих моделях с использованием общего оценщика см. [модели поездов с использованием машинного обучения Azure с использованием оценщика](how-to-train-ml-models.md)

Если ваш учебный скрипт нуждается в дополнительных пакетов пипса или конды для `pip_packages` запуска, вы можете иметь пакеты, установленные на результирующем изображении докера, передавая их имена через `conda_packages` аргументы.

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

Для получения дополнительной информации о настройке среды Python [см.](how-to-use-environments.md)

## <a name="submit-a-run"></a>Отправить пробег

[Объект Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) предоставляет интерфейс истории выполнения во время выполнения и после его завершения.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

При выполнении run он проходит следующие этапы:

- **Подготовка**: Изображение докера создается в соответствии с оценщиком PyTorch. Изображение загружается в реестр контейнеров рабочего пространства и кэшируется для последующих запусков. Логи также передаются в историю выполнения и могут быть просмотрены для мониторинга прогресса.

- **Масштабирование**: Кластер пытается увеличить масштаб, если кластер ИИ пакетов требует больше узлов для выполнения выполнения, чем в настоящее время.

- **Запуск:** Все скрипты в папке скрипта загружаются в цель вычисления, хранилища данных монтируются или копируются, а entry_script выполняется. Выходы из stdout и папки ./logs передаются в историю выполнения и могут использоваться для мониторинга выполнения.

- **Постобработка**: Папка ./выходов запуска скопирована к истории выполнения.

## <a name="register-or-download-a-model"></a>Регистрация или загрузка модели

После того как вы обучили модель, вы можете зарегистрировать его в рабочее пространство. Регистрация моделей позволяет хранить и отстраивать модели в рабочем пространстве для упрощения [управления и развертывания моделей.](concept-model-management-and-deployment.md)

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

> [!TIP]
> Только что зарегистрированная модель развернута точно так же, как и любая другая зарегистрированная модель в Azure Machine Learning, независимо от того, какой оценщик вы использовали для обучения. Способ развертывания содержит раздел о регистрации моделей, но можно перейти непосредственно к [созданию вычислительной цели](how-to-deploy-and-where.md#choose-a-compute-target) для развертывания, так как у вас уже есть зарегистрированная модель.

Вы также можете скачать локальную копию модели с помощью объекта Run. В скрипте `pytorch_train.py`обучения объект сохранения PyTorch сохраняет модель в локальной папке (локальной для цели вычисления). Вы можете использовать объект Run для загрузки копии.

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

Оценщик [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) также поддерживает распределенное обучение по кластерам процессоров и графических процессоров. Вы можете легко запустить распределенные задания PyTorch, и Azure Machine Learning будет управлять оркестровкой для вас.

### <a name="horovod"></a>Horovod
[Хорвод](https://github.com/uber/horovod) является открытым исходным кодом, все сокращают рамки для распределенного обучения, разработанного Uber. Он предлагает легкий путь к распределенной GPU PyTorch рабочих мест.

Чтобы использовать Горовод, [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) укажите `distributed_training` объект для параметра в конструкторе PyTorch. Этот параметр гарантирует, что библиотека Horovod установлена для использования в скрипте обучения.


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
Horovod и его зависимости будут установлены для вас, так `train.py` что вы можете импортировать его в вашем сценарии обучения следующим образом:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Экспорт в ONNX

Чтобы оптимизировать вывод с [ONNX Runtime,](concept-onnx.md)преобразуйте подготовленную модель PyTorch в формат ONNX. Вывод, или оценка модели, — это этап, на котором развернутая модель используется для прогнозирования, чаще всего на производственных данных. Смотрите [учебник](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) для примера.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы обучали и регистрировали глубокую нейронную сеть с помощью PyTorch на Azure Machine Learning. Чтобы узнать, как развернуть модель, перейти к нашей статье развертывания модели.

> [!div class="nextstepaction"]
> [Как и где развертывать модели](how-to-deploy-and-where.md)
* [Отслеживание метрик выполнения во время обучения](how-to-track-experiments.md)
* [Настройка гиперпараметров](how-to-tune-hyperparameters.md)
* [Развертывание обученной модели](how-to-deploy-and-where.md)
* [Справочная архитектура для распределенного глубокого обучения в Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)

