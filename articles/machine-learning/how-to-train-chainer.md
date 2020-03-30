---
title: Поезд глубокого обучения Модели Chainer
titleSuffix: Azure Machine Learning
description: Узнайте, как запускать скрипты обучения PyTorch в корпоративном масштабе, используя класс оценки azure Machine Learning Chainer.  Пример сценария классифицирует рукописные изображения цифр для создания нейронной сети глубокого обучения с помощью библиотеки Chainer Python, работающая поверх numpy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 08/02/2019
ms.openlocfilehash: e2840a6295140e0dc22a032fa844c0488403c5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75536620"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning"></a>Поезд и регистрация моделей Chainer в масштабе с Azure машинного обучения
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье узнайте, как запускать сценарии обучения [Chainer](https://chainer.org/) в корпоративном масштабе, используя класс оценки azure Machine Learning [Chainer.](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) Пример обучающего скрипта в этой статье использует популярный [набор данных MNIST](http://yann.lecun.com/exdb/mnist/) для классификации рукописных цифр с помощью глубокой нейронной сети (DNN), построенной с помощью библиотеки Chainer Python, работая поверх [numpy.](https://www.numpy.org/)

Независимо от того, обучаете ли вы модель глубокого обучения Chainer с нуля или приносите существующую модель в облако, вы можете использовать Azure Machine Learning для масштабирования обучающих заданий с открытым исходным кодом с использованием эластичных облачных вычислительных ресурсов. Можно создавать, развертывать, развертывать и контролировать производственные модели с помощью Машинного обучения Azure. 

Узнайте больше о [глубоком обучении и машинном обучении.](concept-deep-learning-vs-machine-learning.md)

Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree) уже сегодня.

## <a name="prerequisites"></a>Предварительные требования

Выполнить этот код на любой из этих сред:

- Экземпляр вычислений машинного обучения Azure - нет необходимости загружать или установку

    - Завершите [обучение: Настройка среды и рабочего пространства](tutorial-1st-experiment-sdk-setup.md) для создания специального ноутбука сервера предварительно загружены с SDK и образец репозитория.
    - В папке глубокого обучения образцов на блокноте на сервере ноутбука найдите готовый блокнот и файлы в папке **«как к использованию-лазурит» > мл > цепи > развертывание > поезд-гиперпараметр-настройка-развертывание-с-цепной** папкой.  Ноутбук включает в себя расширенные разделы, охватывающие интеллектуальную настройку гиперпараментов, развертывание моделей и виджеты ноутбуков.

- Собственный сервер Jupyter Notebook

    - [Установите SDK машинного обучения Azure.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
    - [Создание файла конфигурации рабочего пространства.](how-to-configure-environment.md#workspace)
    - Скачать образец файла сценария [chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer).
     - Вы также можете найти завершенную [версию Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) этого руководства на странице образцов GitHub. Ноутбук включает в себя расширенные разделы, охватывающие интеллектуальную настройку гиперпараментов, развертывание моделей и виджеты ноутбуков.

## <a name="set-up-the-experiment"></a>Настройка эксперимента

В этом разделе устанавливается учебный эксперимент путем загрузки необходимых пакетов питона, инициализации рабочего пространства, создания эксперимента и загрузки обучающих данных и обучающих скриптов.

### <a name="import-packages"></a>Импорт пакетов

Во-первых, импортируйте библиотеку Azureml.core Python и отобразите номер версии.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Инициализация рабочего пространства

[Рабочее пространство Azure Machine Learning](concept-workspace.md) — это ресурс высшего уровня службы. Он предоставляет вам централизованное место для работы со всеми артефактами, которые вы создаете. В Python SDK можно получить доступ к артефактам рабочего пространства, создав [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) объект.

Создайте объект рабочей `config.json` области, прочитав файл, созданный в [разделе предпосылок:](#prerequisites)

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Создание каталога проектов
Создайте каталог, который будет содержать весь необходимый код из локальной машины, к которым вам потребуется доступ на удаленном ресурсе. Это включает в себя сценарий обучения и любые дополнительные файлы, от них зависит сценарий обучения.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Подготовка сценария обучения

В этом учебнике, учебный сценарий **chainer_mnist.py** уже предусмотрено для вас. На практике вы должны иметь возможность принимать любой пользовательский сценарий обучения, как есть, и запускать его с Azure ML без необходимости изменять код.

Чтобы использовать возможности отслеживания и метрик Azure ML, добавьте небольшое количество кода Azure ML в свой обучающий скрипт.  Сценарий обучения **chainer_mnist.py** показывает, как войти некоторые метрики в ваш Azure ML запустить с помощью `Run` объекта в скрипте.

В предоставленном обучаемом скрипте `datasets.mnist.get_mnist` используются примеры данных из функции цепи.  Для собственных данных может потребоваться использовать такие этапы, как [набор данных upload и скрипты,](how-to-train-keras.md#data-upload) чтобы сделать данные доступными во время обучения.

Копируйте сценарий обучения **chainer_mnist.py** в каталог проекта.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-a-deep-learning-experiment"></a>Создание эксперимента по глубокому обучению

Создайте эксперимент. В этом примере создайте эксперимент под названием "chainer-mnist".

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Создание или создание вычислительной цели

Вам нужна [вычислительная цель](concept-compute-target.md) для обучения модели. В этом примере используется управляемый Azure ML вычислений (AmlCompute) для удаленного учебного вычислительного ресурса.

**Создание AmlCompute занимает около 5 минут.** Если AmlCompute с таким именем уже находится в вашем рабочем пространстве, этот код пропускает процесс создания.  

```Python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current cluster. 
print(compute_target.get_status().serialize())
```

Для получения дополнительной информации о [what is a compute target](concept-compute-target.md) целевых показателях вычислений см.

## <a name="create-a-chainer-estimator"></a>Создание оценщика Chainer

[Оценщик Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) предоставляет простой способ запуска рабочих мест обучения Chainer на вашей вычислительной цели.

Оценщик Chainer реализуется [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) через общий класс, который может быть использован для поддержки любых рамок. Для получения дополнительной информации о обучающих моделях с использованием общего оценщика см. [модели поездов с использованием машинного обучения Azure с использованием оценщика](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import Chainer

script_params = {
    '--epochs': 10,
    '--batchsize': 128,
    '--output_dir': './outputs'
}

estimator = Chainer(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    pip_packages=['numpy', 'pytest'],
                    entry_script='chainer_mnist.py',
                    use_gpu=True)
```

## <a name="submit-a-run"></a>Отправить пробег

[Объект Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) предоставляет интерфейс истории выполнения во время выполнения и после его завершения.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

При выполнении run он проходит следующие этапы:

- **Подготовка**: Изображение докера создается в соответствии с оценщиком Chainer. Изображение загружается в реестр контейнеров рабочего пространства и кэшируется для последующих запусков. Логи также передаются в историю выполнения и могут быть просмотрены для мониторинга прогресса.

- **Масштабирование**: Кластер пытается увеличить масштаб, если кластер ИИ пакетов требует больше узлов для выполнения выполнения, чем в настоящее время.

- **Запуск:** Все скрипты в папке скрипта загружаются в цель вычисления, хранилища данных монтируются или копируются, а entry_script выполняется. Выходы из stdout и папки ./logs передаются в историю выполнения и могут использоваться для мониторинга выполнения.

- **Постобработка**: Папка ./выходов запуска скопирована к истории выполнения.

## <a name="save-and-register-the-model"></a>Сохранить и зарегистрировать модель

После того как вы обучили модель, вы можете сохранить и зарегистрировать его в рабочее пространство. Регистрация моделей позволяет хранить и отстраивать модели в рабочем пространстве для упрощения [управления и развертывания моделей.](concept-model-management-and-deployment.md)


После завершения обучения модели зарегистрируйте модель в рабочем пространстве со следующим кодом.  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> Только что зарегистрированная модель развернута точно так же, как и любая другая зарегистрированная модель в Azure Machine Learning, независимо от того, какой оценщик вы использовали для обучения. Способ развертывания содержит раздел о регистрации моделей, но можно перейти непосредственно к [созданию вычислительной цели](how-to-deploy-and-where.md#choose-a-compute-target) для развертывания, так как у вас уже есть зарегистрированная модель.

Вы также можете скачать локальную копию модели. Это может быть полезно для выполнения дополнительной работы проверки модели локально. В скрипте `chainer_mnist.py`обучения объект сохранения сохраняет модель в локальной папке (локальной для целевой вычислить). Вы можете использовать объект Run для загрузки копии из хранилища данных.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы обучали и регистрировали глубокую нейронную сеть с помощью Chainer на Azure Machine Learning. Чтобы узнать, как развернуть модель, перейти к нашей статье [развертывания модели.](how-to-deploy-and-where.md)

* [Настройка гиперпараметров](how-to-tune-hyperparameters.md)

* [Отслеживание метрик выполнения во время обучения](how-to-track-experiments.md)

* [Просмотр нашей справочной архитектуры для распределенного обучения глубокому обучению в Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
