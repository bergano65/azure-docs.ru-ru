---
title: Обучение и зарегистрируйте модели формирователя цепочки
titleSuffix: Azure Machine Learning service
description: В этой статье показано, как обучать и зарегистрируйте модели формирователя цепочки с помощью службы машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 06/15/2019
ms.openlocfilehash: 5057a8cf6c8769761a1a1a9f513ec46d152289ab
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67488669"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning-service"></a>Обучение и зарегистрировать формирователя цепочки модели в масштабе с помощью службы машинного обучения Azure

В этой статье показано, как обучать и зарегистрируйте модели формирователя цепочки с помощью службы машинного обучения Azure. Она использует популярного [набора данных MNIST](http://yann.lecun.com/exdb/mnist/) для классификации рукописных цифр, с помощью глубокой нейронной сети (DNN), созданные с помощью [библиотеки Python формирователя цепочки](https://Chainer.org) под управлением на основе [numpy](https://www.numpy.org/).

Формирователя цепочки — это высокоуровневый API, поддерживающих работу в верхней части других популярных платформ DNN для упрощения разработки нейронной сети. С помощью службы машинного обучения Azure вы можете быстро выполнить развертывание заданий обучения, с помощью эластичных облачных вычислительных ресурсов. Вы также можете отслеживать свои учебных запусков, моделях версии развертывание моделей и многое другое.

При разработке модели формирователя цепочки из нуля, или вам нужно добавить существующую модель в облаке, службы машинного обучения Azure поможет вам создавать готовые модели.

Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Технические условия

Выполните этот код на любой из этих сред:

- Machine Learning записные книжки Azure виртуальной Машины — нет файлы для загрузки или установки не требуется

    - Завершить [быстрого запуска облачной записной книжки](quickstart-run-cloud-notebook.md) создание специальных ноутбуков сервера предварительно загруженным с помощью пакета SDK и репозиторий с примером.
    - В папке samples на сервере записной книжки, найти завершенного записной книжки и файлы в **how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer** папки.  Записная книжка включает расширенный, освещая интеллектуальной настройки гиперпараметров, развертывание модели и записной книжки мини-приложения.

- Собственный сервер Jupyter Notebook

    - [Установка Azure Machine Learning пакета SDK для Python](setup-create-workspace.md#sdk)
    - [Создайте файл конфигурации рабочей области](setup-create-workspace.md#write-a-configuration-file)
    - Скачать пример файла сценария [chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/chainer_mnist.py)
     - Вы также найдете завершенного [версии записной книжки Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) данного руководства на странице образцов GitHub. Записная книжка включает расширенный, освещая интеллектуальной настройки гиперпараметров, развертывание модели и записной книжки мини-приложения.

## <a name="set-up-the-experiment"></a>Настроить эксперимент

В этом разделе устанавливает обучающий эксперимент с помощью загрузки пакетов требуется python, инициализация рабочей области, создания эксперимента и отправки данных для обучения и скриптов обучения.

### <a name="import-packages"></a>Импорт пакетов

Во-первых импортируйте azureml.core показ рекламы библиотеки Python номер версии.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Инициализация рабочей области

[Рабочей области машинного обучения Azure службы](concept-workspace.md) — это ресурс верхнего уровня для службы. Он предоставляет централизованное расположение для работы с все артефакты, созданные. В пакете SDK для Python, можно получить доступ к артефактам рабочей области путем создания [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) объекта.

Создать объект рабочей области из `config.json` файла, созданного в [предварительных](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Создайте каталог проекта
Создайте каталог, который будет содержать весь необходимый код с локального компьютера, что вам потребуется доступ к на удаленный ресурс. Это включает в себя скрипт обучения и дополнительных файлов, от которых зависит ваш скрипт обучения.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Подготовка сценария обучения

В этом руководстве скрипт обучения **chainer_mnist.py** уже существующей. На практике можно выполнить любой сценарий курс и запустите его с помощью машинного Обучения Azure без необходимости изменения кода.

Чтобы использовать Azure ML возможности отслеживания и метрики, необходимо добавить небольшой фрагмент кода машинного Обучения Azure в сценарий обучения.  Сценарий обучения **chainer_mnist.py** показано, как в журнал некоторые показатели вашей запуска студии машинного обучения Azure. Чтобы сделать это, доступ к Azure ML `Run` объект в скрипте.

Скопируйте сценарий обучения **chainer_mnist.py** в каталоге проекта.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-an-experiment"></a>Создание эксперимента

Создание эксперимента и папку для хранения сценариев обучения. В этом примере Создайте эксперимент, который называется «формирователя цепочки mnist».

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Создать или получить целевой объект вычислений

Необходимо будет [целевого объекта вычислений](concept-compute-target.md) для обучения модели. В этом руководстве используется вычислений машинного Обучения Azure управляемых (AmlCompute) для ресурса вычислений удаленное обучение.

**Создание из AmlCompute занимает примерно 5 минут**. Если AmlCompute с таким именем уже существует в рабочей области, этот код будет пропускать в процессе создания.  

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

Дополнительные сведения о целевых объектах вычислений см. в разделе [Какова целевого объекта вычислений](concept-compute-target.md) статьи.

## <a name="create-a-chainer-estimator"></a>Создание оценки формирователя цепочки

[Оценщика формирователя цепочки](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) предоставляет простой способ запуска заданий обучения формирователя цепочки на целевого объекта вычислений.

Оценщик формирователя цепочки реализуется с помощью универсального [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) класс, который может использоваться для поддержки любой платформы. Дополнительные сведения об обучении моделей с помощью универсальный механизм оценки, см. в разделе [обучения моделей с машинным обучением Azure, с помощью механизма оценки](how-to-train-ml-models.md)

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

## <a name="submit-a-run"></a>Запуске

[Выполнения объекта](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) предоставляет интерфейс для журнала выполнения, пока выполняется задание, и после его завершения.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Как выполняется запуска, он проходит через следующие этапы:

- **Подготовка**: В соответствии с оценщика формирователя цепочки создается образ docker. Изображение отправлены в реестр контейнеров в рабочей области и кэшируются для выполнения более поздней версии. Также будут отправлены в журнал выполнения и отслеживать ход выполнения можно просмотреть журналы.

- **Масштабирование**. Кластера пытается увеличить масштаб, если для кластера Batch AI требуется больше узлов для выполнения запуска, чем в настоящее время доступны.

- **Running**. Все сценарии в папке сценария передаются целевого объекта вычислений, хранилищ данных отправляются подключен или копируются, а также выполняется entry_script. Выходные данные stdout и. / Папка журналов будут отправлены в журнал выполнения и может использоваться для отслеживания выполнения.

- **Постобработка**. . / Outputs папки выполнения, копируются в журнал выполнения.

## <a name="save-and-register-the-model"></a>Сохранение и регистрация модели

После обучения модели, можно сохранить и зарегистрировать его в рабочую область. Регистрация модели позволяет хранилища и версию модели в рабочей области для упрощения [моделей управления и развертывания](concept-model-management-and-deployment.md).

Добавьте следующий код в скрипт обучения **chainer_mnist.py**, чтобы сохранить модель. 

``` Python
    serializers.save_npz(os.path.join(args.output_dir, 'model.npz'), model)
```

Зарегистрируйте модель в рабочую область на приведенный ниже.

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```



## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы Обучите модель формирователя цепочки в службе машинного обучения Azure. 

* Чтобы узнать, как развернуть модель, перейдите к нашей [развертывание модели](how-to-deploy-and-where.md) статьи.

* [Настройка гиперпараметров](how-to-tune-hyperparameters.md)

* [Отслеживание метрик выполнения во время обучения](how-to-track-experiments.md)
