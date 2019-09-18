---
title: Обучение нейронной сети для глубокого обучения с помощью формирователя цепочки
titleSuffix: Azure Machine Learning
description: Узнайте, как выполнять сценарии обучения PyTorch в масштабе предприятия с помощью класса оценщика цепочки Машинное обучение Azure.  Пример сценария классифицирует рукописные цифры для создания нейронной сети глубокого обучения с помощью библиотеки Python на основе NumPy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 08/02/2019
ms.openlocfilehash: 79c0cadc1b266a6d160cd36fc21dcaf36637a2b1
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076421"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning"></a>Обучение и регистрация моделей цепочек в масштабе с помощью Машинное обучение Azure

Из этой статьи вы узнаете, как выполнять сценарии обучения для программы [цепочка](https://chainer.org/) в масштабах предприятия с помощью класса [оценщика цепочки](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) машинное обучение Azure. В примере обучающего скрипта в этой статье используется популярный [набор данных MNIST](http://yann.lecun.com/exdb/mnist/) для классификации рукописных цифр с помощью глубокой нейронной сети (DNN), созданной с помощью библиотеки Python, которая выполняется поверх [NumPy](https://www.numpy.org/).

Независимо от того, обходите ли вы модель цепочки глубокого обучения с нуля или используете существующую модель в облаке, вы можете использовать Машинное обучение Azure для масштабирования заданий обучения с открытым исходным кодом с помощью эластичных облачных ресурсов. Вы можете создавать, развертывать, выполнять версии и отслеживать модели производственного уровня с помощью Машинное обучение Azure. 

Дополнительные сведения о [глубоком обучении и машинном обучении](concept-deep-learning-vs-machine-learning.md).

Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию машинное обучение Azure](https://aka.ms/AMLFree) уже сегодня.

## <a name="prerequisites"></a>Предварительные требования

Запустите этот код в любой из этих сред:

- Машинное обучение Azure виртуальной машины записной книжки — Загрузка или установка не требуется

    - Пройдите [руководство по Настройте среду и рабочую](tutorial-1st-experiment-sdk-setup.md) область, чтобы создать выделенный сервер записной книжки, предварительно загруженный с помощью пакета SDK и примера репозитория.
    - В папке примеры глубокого обучения на сервере записной книжки найдите готовую записную книжку и файлы в папке " **инструкции-azureml/Training-with-глубокая/Learning-с параметром-"-redeploy-с-** ".  Записная книжка включает в себя развернутые разделы, охватывающие интеллектуальные настройки, развертывание моделей и мини-приложения записных книжек.

- Собственный сервер Jupyter Notebook

    - [Установите пакет SDK для машинное обучение Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Создайте файл конфигурации рабочей области](how-to-configure-environment.md#workspace).
    - Скачайте пример файла скрипта [chainer_mnist. корректировки](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/chainer_mnist.py).
     - Вы также можете найти завершенную [Jupyter Notebook версию](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) этого руководства на странице примеров GitHub. Записная книжка включает в себя развернутые разделы, охватывающие интеллектуальные настройки, развертывание моделей и мини-приложения записных книжек.

## <a name="set-up-the-experiment"></a>Настройка эксперимента

В этом разделе выполняется настройка обучающего эксперимента путем загрузки требуемых пакетов Python, инициализации рабочей области, создания эксперимента и отправки обучающих данных и сценариев обучения.

### <a name="import-packages"></a>Импорт пакетов

Сначала импортируйте библиотеку azureml. Core Python и отобразите номер версии.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Инициализация рабочей области

[Машинное обучение Azure Рабочая область](concept-workspace.md) — это ресурс верхнего уровня для службы. Он обеспечивает централизованное расположение для работы со всеми создаваемыми артефактами. В пакете SDK для Python можно получить доступ к артефактам рабочей области, [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) создав объект.

Создайте объект рабочей области, читая `config.json` файл, созданный в [разделе Предварительные требования](#prerequisites):

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Создание каталога проекта
Создайте каталог, который будет содержать весь необходимый код с локального компьютера, к которому потребуется доступ на удаленном ресурсе. Сюда входит сценарий обучения и все дополнительные файлы, от которых зависит ваш сценарий обучения.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Подготовка сценария обучения

В этом руководстве обучающий сценарий **chainer_mnist. Корректировка** уже предоставлен. На практике вы можете использовать любой пользовательский сценарий обучения как есть и запускать его с помощью Azure ML без необходимости изменять код.

Чтобы использовать возможности отслеживания и метрик машинного обучения Azure, добавьте небольшой объем кода машинного обучения Azure в сценарий обучения.  В обучающем скрипте **chainer_mnist. копировать** показано, как зарегистрировать некоторые метрики в машинном обучении Azure с помощью `Run` объекта в скрипте.

Предоставленный Скрипт обучения использует примеры данных из `datasets.mnist.get_mnist` функции Chain.  Для собственных данных может потребоваться выполнить такие действия, как [Передача набора данных и скриптов](how-to-train-keras.md) , чтобы сделать данные доступными во время обучения.

Скопируйте обучающий сценарий **chainer_mnist.** Copy в каталог проекта.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-a-deep-learning-experiment"></a>Создание эксперимента глубокого обучения

Создайте эксперимент. В этом примере Создайте эксперимент с именем "Chain-mnist".

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Создание или получение целевого объекта вычислений

Для обучения модели требуется [целевой объект вычислений](concept-compute-target.md) . В этом примере используется управляемый центр МАШИНного обучения Azure (Амлкомпуте) для удаленного обучающего ресурса.

**Создание амлкомпуте занимает примерно 5 минут**. Если Амлкомпуте с таким именем уже находится в рабочей области, этот код пропускает процесс создания.  

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

Дополнительные сведения о целевых объектах вычислений см. в статье [что такое целевые показатели вычислений](concept-compute-target.md) .

## <a name="create-a-chainer-estimator"></a>Создание оценщика цепочки

Средство [оценки формирователя цепочки](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) предоставляет простой способ запуска заданий обучения формирователя цепочки на целевом объекте вычислений.

Оценщик цепочки реализуется через универсальный [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) класс, который можно использовать для поддержки любой платформы. Дополнительные сведения о учебных моделях с помощью универсального средства оценки см. в статье [обучение моделей с машинное обучение Azure помощью средства оценки](how-to-train-ml-models.md) .

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

## <a name="submit-a-run"></a>Отправка запуска

[Объект Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) предоставляет интерфейс для журнала выполнения во время выполнения задания и после его завершения.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

При выполнении выполнения он проходит следующие этапы.

- **Идет подготовка**: Образ DOCKER создается в соответствии с оценщиком цепочки. Образ отправляется в реестр контейнеров рабочей области и кэшируется для последующего выполнения. Журналы также передаются в журнал выполнения и могут быть просмотрены для отслеживания хода выполнения.

- **Масштабирование**. Кластер пытается выполнить масштабирование, если кластеру Batch AI требуется больше узлов для выполнения выполнения, чем доступно в данный момент.

- **Running**. Все скрипты в папке скрипта передаются в целевой объект вычислений, хранилища данных подключаются или копируются, и выполняется entry_script. Выходные данные из STDOUT и папки/логс передаются в журнал выполнения и могут использоваться для наблюдения за выполнением.

- **Постобработка**. Папка./Outputs выполнения копируется в журнал выполнения.

## <a name="save-and-register-the-model"></a>Сохранение и регистрация модели

После обучения модели ее можно сохранить и зарегистрировать в рабочей области. Регистрация модели позволяет хранить и отменять версии моделей в рабочей области, чтобы упростить [Управление моделями и их развертывание](concept-model-management-and-deployment.md).


После завершения обучения модели Зарегистрируйте модель в рабочей области с помощью следующего кода.  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> Если появится сообщение об ошибке, что модель не найдена, придайте ей минуту и повторите попытку.  Иногда существует небольшая задержка между окончанием обучающего запуска и доступностью модели в каталоге Outputs.

Также можно скачать локальную копию модели. Это может быть полезно для выполнения дополнительной проверки модели локально. В обучающем скрипте `chainer_mnist.py`объект хранителя сохраняет модель в локальной папке (локальную для целевого объекта вычислений). Чтобы скачать копию из хранилища данных, можно использовать объект Run.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Следующие шаги

В этой статье вы обучили и зарегистрировали нейронную сеть для глубокого обучения, использующую цепочку Машинное обучение Azure. Чтобы узнать, как развернуть модель, перейдите к статье о [развертывании модели](how-to-deploy-and-where.md) .

* [Настройка гиперпараметров](how-to-tune-hyperparameters.md)

* [Отслеживание метрик выполнения во время обучения](how-to-track-experiments.md)

* [Ознакомьтесь с нашей справочной архитектурой по распределенному глубокому обучению в Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
