---
title: Обучение моделей PyTorch для глубокого обучения
titleSuffix: Azure Machine Learning
description: Узнайте, как выполнять сценарии обучения PyTorch в масштабе предприятия с помощью Машинное обучение Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: b03395b9c615466a4d64d8760db8ac23a040d832
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360944"
---
# <a name="train-pytorch-models-at-scale-with-azure-machine-learning"></a>Обучение моделей PyTorch в масштабе с помощью Машинное обучение Azure

Из этой статьи вы узнаете, как выполнять сценарии обучения [PyTorch](https://pytorch.org/) в масштабе предприятия с помощью машинное обучение Azure.

Примеры сценариев в этой статье используются для классификации образов Chicken и Турция для создания нейронной сети глубокого обучения (DNN) на основе [учебника](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)по переносу PyTorch. 

Независимо от того, где вы изучаете модель PyTorch глубокого обучения или используете существующую модель в облаке, вы можете использовать Машинное обучение Azure для масштабирования заданий обучения с открытым исходным кодом с помощью эластичных облачных ресурсов. Вы можете создавать, развертывать, выполнять версии и отслеживать модели производственного уровня с помощью Машинное обучение Azure. 

## <a name="prerequisites"></a>Обязательные условия

Запустите этот код в любой из этих сред:

- Вычислительная операция Машинного обучения Azure — загрузка или установка не требуется

    - Пройдите [руководство по настройке среды и рабочей области](tutorial-1st-experiment-sdk-setup.md), чтобы создать выделенный сервер записных книжек, предварительно загруженный с пакетом SDK и репозиторием с примером.
    - В папке примеры глубокого обучения на сервере записной книжки найдите готовую и развернутую записную книжку, перейдя к этому каталогу: **практические советы по использованию azureml > ML-frameworks > pytorch > обучения-параметры-Настройка-Deploy-with-pytorch** Folder. 
 
 - Собственный сервер Jupyter Notebook
    - [Установите пакет SDK для машинное обучение Azure](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.15.0).
    - [Создайте файл конфигурации рабочей области](how-to-configure-environment.md#workspace).
    - [Загрузка примеров файлов скриптов](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    Вы также можете найти завершенную [Jupyter Notebook версию](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) этого руководства на странице примеров GitHub. Записная книжка включает в себя развернутые разделы, охватывающие интеллектуальные настройки, развертывание моделей и мини-приложения записных книжек.

## <a name="set-up-the-experiment"></a>Настройка эксперимента

В этом разделе выполняется настройка обучающего эксперимента путем загрузки требуемых пакетов Python, инициализации рабочей области, создания целевого объекта вычислений и определения среды обучения.

### <a name="import-packages"></a>Импорт пакетов

Сначала импортируйте необходимые библиотеки Python.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment
from azureml.core import Environment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Инициализация рабочей области

[Машинное обучение Azure Рабочая область](concept-workspace.md) — это ресурс верхнего уровня для службы. Она предоставляет централизованное расположение для работы со всеми создаваемыми артефактами. В пакете SDK для Python можно получить доступ к артефактам рабочей области, создав [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) объект.

Создайте объект рабочей области из `config.json` файла, созданного в [разделе Предварительные требования](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="get-the-data"></a>Получение данных

Набор данных состоит из примерно 120 учебных изображений для туркэйс и чиккенс с образами проверки 100 для каждого класса. Мы будем скачивать и извлекать набор данных в рамках нашего обучающего сценария `pytorch_train.py` . Изображения являются подмножеством [набора данных Open Images V5](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-script"></a>Подготовка сценария обучения

В этом руководстве обучающий сценарий `pytorch_train.py` уже предоставлен. На практике вы можете использовать любой пользовательский сценарий обучения, как есть, и запустить его с помощью Машинное обучение Azure.

Создайте папку для учебных сценариев.

```python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)
shutil.copy('pytorch_train.py', project_folder)
```

### <a name="create-a-compute-target"></a>Создание целевого объекта вычислений

Создайте целевой объект вычислений для выполнения задания PyTorch. В этом примере создайте кластерный Машинное обучение Azure вычислений с поддержкой GPU.

```Python
cluster_name = "gpu-cluster"

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

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

Дополнительные сведения о целевых объектах вычислений см. в статье [что такое целевые показатели вычислений](concept-compute-target.md) .

### <a name="define-your-environment"></a>Определение среды

Чтобы определить [среду](concept-environments.md) машинного обучения Azure, которая инкапсулирует зависимости сценария обучения, можно либо определить пользовательскую среду, либо использовать проверенную среду машинного обучения Azure.

#### <a name="use-a-curated-environment"></a>Использование проверенной среды
Azure ML предоставляет готовые, проверенные среды, если вы не хотите определять собственную среду. В МАШИНном обучении Azure имеется несколько проверенных сред ЦП и GPU для PyTorch, соответствующих разным версиям PyTorch. Дополнительные сведения см. [здесь](resource-curated-environments.md).

Если вы хотите использовать проверенную среду, то вместо этого можно выполнить следующую команду:

```python
curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
```

Чтобы просмотреть пакеты, входящие в проверенную среду, можно записать зависимости conda на диск:
```python
pytorch_env.save_to_directory(path=curated_env_name)
```

Убедитесь, что проверенная среда включает все зависимости, необходимые для обучающего скрипта. В противном случае необходимо будет изменить окружение, включив недостающие зависимости. Обратите внимание, что при изменении среды потребуется присвоить ей новое имя, так как префикс AzureML зарезервирован для проверенных сред. Если вы изменили файл YAML Dependencies conda, можно создать новое окружение с новым именем, например:
```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')
```

Если вы изменили проверенный объект среды напрямую, вы можете клонировать эту среду с новым именем:
```python
pytorch_env = pytorch_env.clone(new_name='pytorch-1.6-gpu')
```

#### <a name="create-a-custom-environment"></a>Создание пользовательской среды

Вы также можете создать собственную среду машинного обучения Azure, которая инкапсулирует зависимости обучающего сценария.

Сначала определите зависимости conda в файле YAML. в этом примере файл называется `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - torch==1.6.0
  - torchvision==0.7.0
  - future==0.17.1
  - pillow
```

Создайте среду машинного обучения Azure из этой спецификации среды conda. Среда будет упакована в контейнер DOCKER во время выполнения.

По умолчанию, если базовый образ не указан, Azure ML будет использовать образ ЦП в `azureml.core.environment.DEFAULT_CPU_IMAGE` качестве базового образа. Поскольку в этом примере выполняется обучение на кластере GPU, необходимо указать базовый образ GPU с необходимыми драйверами и зависимостями GPU. Служба машинного обучения Azure поддерживает набор базовых образов, опубликованных в реестре контейнеров Microsoft (мкр), которые можно использовать. Дополнительные сведения см. в репозитории GitHub [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) .

```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
pytorch_env.docker.enabled = True
pytorch_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> При необходимости можно просто захватить все зависимости непосредственно в пользовательском образе DOCKER или Dockerfile и создать среду из нее. Дополнительные сведения см. в разделе [обучение с помощью пользовательского образа](how-to-train-with-custom-image.md).

Дополнительные сведения о создании и использовании сред см. [в разделе Создание и использование программных сред в машинное обучение Azure](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Настройка и отправка учебного запуска

### <a name="create-a-scriptrunconfig"></a>Создание Скриптрунконфиг

Создайте объект [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py), чтобы указать сведения о конфигурации для вашего задания обучения, в том числе скрипт обучения, используемую среду и целевой объект вычислений, на котором будет выполняться задание. Любые аргументы в скрипте обучения передаются через командную строку, если они указаны в `arguments` параметре. 

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_train.py',
                      arguments=['--num_epochs', 30, '--output_dir', './outputs'],
                      compute_target=compute_target,
                      environment=pytorch_env)
```

> [!WARNING]
> Машинное обучение Azure запускает скрипты обучения, копируя весь исходный каталог. Если у вас есть конфиденциальные данные, которые не нужно передавать, используйте [файл. Ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) или не включайте его в исходный каталог. Вместо этого получите доступ к данным с помощью [набора данных](how-to-train-with-datasets.md)машинного обучения Azure.

Дополнительные сведения о настройке заданий с помощью Скриптрунконфиг см. в статье [Настройка и отправка обучающих запусков](how-to-set-up-training-targets.md).

> [!WARNING]
> Если вы ранее использовали оценщик PyTorch для настройки заданий обучения PyTorch, обратите внимание, что оценивающие будут считаться устаревшими в будущих выпусках пакета Azure ML SDK. С помощью пакета SDK машинного обучения Azure >= 1.15.0, Скриптрунконфиг является рекомендуемым способом настройки заданий обучения, включая те, которые используют платформы DL.

## <a name="submit-your-run"></a>Отправка выполнения

[Объект Run](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) предоставляет интерфейс для журнала выполнения во время выполнения задания и после его завершения.

```Python
run = Experiment(ws, name='pytorch-birds').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Что происходит во время выполнения
При выполнении выполнения он проходит следующие этапы.

- **Подготовка**. образ DOCKER создается в соответствии с определенной средой. Образ отправляется в реестр контейнеров рабочей области и кэшируется для последующего выполнения. Журналы также передаются в журнал выполнения и могут быть просмотрены для отслеживания хода выполнения. Если вместо этого указана проверенная среда, то будет использоваться кэшированный образ, в котором выполняется резервное копирование проверенной среды.

- **Масштабирование**. Кластер пытается выполнить масштабирование, если кластеру Batch AI требуется больше узлов для выполнения выполнения, чем в настоящее время доступно.

- **Выполняется** : все скрипты в папке Script передаются в целевой объект вычислений, хранилища данных подключаются или копируются, и выполняется `script` . Выходные данные из STDOUT и папки **/логс** передаются в журнал выполнения и могут использоваться для наблюдения за выполнением.

- **Пост-обработка**. папка **/Outputs** для выполнения копируется в журнал выполнения.

## <a name="register-or-download-a-model"></a>Регистрация или скачивание модели

После обучения модели ее можно зарегистрировать в рабочей области. Регистрация модели позволяет хранить и редактировать версии моделей в рабочей области для упрощения [развертывания и управления моделями](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pytorch-birds', model_path='outputs/model.pt')
```

> [!TIP]
> Руководство по развертыванию содержит раздел, посвященный регистрации моделей, но можно сразу перейти к [созданию целевого объекта вычислений](how-to-deploy-and-where.md#choose-a-compute-target) для развертывания, так как у вас уже есть Зарегистрированная модель.

Вы также можете скачать локальную копию модели с помощью объекта Run. В обучающем скрипте `pytorch_train.py` объект PyTorch Save сохраняет модель в локальную папку (локальную для целевого объекта вычислений). Для скачивания копии можно использовать объект Run.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

# Download the model from run history
run.download_file(name='outputs/model.pt', output_file_path='./model/model.pt'), 
```

## <a name="distributed-training"></a>Распределенное обучение

Машинное обучение Azure также поддерживает распределенные задания PyTorch с несколькими узлами, чтобы можно было масштабировать рабочие нагрузки для обучения. Вы можете легко выполнять распределенные задания PyTorch, и МАШИНное обучение Azure будет управлять согласованием.

Azure ML поддерживает выполнение распределенных заданий PyTorch с помощью встроенного модуля Дистрибутеддатапараллел хоровод и PyTorch.

### <a name="horovod"></a>Horovod
[Хоровод](https://github.com/uber/horovod) — это платформа с открытым исходным кодом, которая сокращает структуру для распределенного обучения, разработанного Uber. Она предлагает простой путь для написания распределенного кода PyTorch для обучения.

Обучающий код должен быть оснащен хоровод для распределенного обучения. Дополнительные сведения об использовании хоровод с PyTorch см. в [документации по хоровод](https://horovod.readthedocs.io/en/stable/pytorch.html).

Кроме того, убедитесь, что в среде обучения включен пакет **хоровод** . Если вы используете PyTorch проверенную среду, хоровод уже включен в качестве одной из зависимостей. Если вы используете собственную среду, убедитесь, что включена зависимость хоровод, например:

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - torch==1.6.0
  - torchvision==0.7.0
  - horovod==0.19.5
```

Чтобы выполнить распределенное задание с использованием MPI или хоровод в МАШИНном обучении Azure, необходимо указать [мпиконфигуратион](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py) в `distributed_job_config` параметре конструктора скриптрунконфиг. Приведенный ниже код настроит распределенное задание с 2 узлами, выполняющее один процесс на каждом узле. Если вы также хотите запустить несколько процессов на одном узле (т. е. Если номер SKU кластера имеет несколько GPU), дополнительно укажите `process_count_per_node` параметр в мпиконфигуратион (значение по умолчанию — `1` ).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_horovod_mnist.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Полный учебник по запуску распределенных PyTorch с помощью хоровод в МАШИНном обучении Azure см. в разделе [Распределенная PyTorch с хоровод](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-horovod).

### <a name="distributeddataparallel"></a>дистрибутеддатапараллел
При использовании встроенного модуля [Дистрибутеддатапараллел](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html) PyTorch, созданного с помощью пакета **Torch. Distributed** в вашем обучающем коде, можно также запустить распределенное задание с помощью Azure ml.

Чтобы запустить распределенное задание PyTorch с помощью Дистрибутеддатапараллел, укажите [питорчконфигуратион](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?preserve-view=true&view=azure-ml-py) в качестве `distributed_job_config` параметра конструктора скриптрунконфиг. Чтобы использовать серверную часть НККЛ для Torch. Distributed, укажите `communication_backend='Nccl'` в питорчконфигуратион. В приведенном ниже коде будет настроено распределенное задание с двумя узлами. Серверная часть НККЛ является рекомендуемой серверной частью для обучения распределенного GPU PyTorch.

Для распределенных заданий PyTorch, настроенных с помощью Питорчконфигуратион, Azure ML установит следующие переменные среды на узлах целевого объекта вычислений:

* `AZ_BATCHAI_PYTORCH_INIT_METHOD`: URL-адрес для инициализации общей файловой системы группы процессов
* `AZ_BATCHAI_TASK_INDEX`: Глобальный ранг рабочего процесса

Эти переменные среды можно указать для соответствующих аргументов обучающего скрипта с помощью `arguments` параметра скриптрунконфиг.

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

args = ['--dist-backend', 'nccl',
        '--dist-url', '$AZ_BATCHAI_PYTORCH_INIT_METHOD',
        '--rank', '$AZ_BATCHAI_TASK_INDEX',
        '--world-size', 2]

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=PyTorchConfiguration(communication_backend='Nccl', node_count=2))
```

Если вы хотите использовать серверную часть глу для распределенного обучения, укажите `communication_backend='Gloo'` вместо этого. Для распределенного обучения ЦП рекомендуется использовать серверную часть глу.

Полный учебник по запуску распределенной PyTorch в МАШИНном обучении Azure см. в разделе [Распределенная PyTorch с помощью дистрибутеддатапараллел](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-nccl-gloo).

## <a name="export-to-onnx"></a>Экспорт в формат ONNX

Чтобы оптимизировать вывод с помощью [среды выполнения ONNX](concept-onnx.md), преобразуйте обученную модель PyTorch в формат ONNX. Вывод или оценка модели — это этап, в котором развернутая модель используется для прогнозирования, чаще всего в рабочих данных. Пример см. в [руководстве](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) .

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы обучили и зарегистрировали нейронную сеть глубокого обучения с помощью PyTorch на Машинное обучение Azure. Чтобы узнать, как развернуть модель, перейдите к статье о развертывании модели.

* [Как и где развертываются модели](how-to-deploy-and-where.md)
* [Отслеживание метрик выполнения во время обучения](how-to-track-experiments.md)
* [Настройка гиперпараметров](how-to-tune-hyperparameters.md)
* [Развертывание обученной модели](how-to-deploy-and-where.md)
* [Эталонная архитектура для распределенного обучения глубокого обучения в Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
