---
title: Обучение моделей машинного обучения с использованием средства оценки
titleSuffix: Azure Machine Learning
description: Узнайте, как выполнять распределенное обучение с одним узлом для традиционного машинного обучения и моделей глубокого обучения с помощью класса "Оценщик" Машинного обучения Azure
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 9f63b4215e9b4a67a439e47501876d237a6d3c3b
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320924"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Обучение моделей с помощью оценщика Машинного обучения Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

С помощью Машинного обучения Azure можно легко отправить скрипт обучения в [различные целевые объекты вычислений](how-to-set-up-training-targets.md#compute-targets-for-training), используя объекты [RunConfiguration](how-to-set-up-training-targets.md#whats-a-run-configuration) и [ScriptRunConfig](how-to-set-up-training-targets.md#submit). Этот шаблон обеспечивает высокую гибкость и максимальный контроль.


Класс оценщика облегчает обучение моделей с глубоким обучением и подкреплением. Он предоставляет высокоуровневую абстракцию, позволяющую легко создавать конфигурацию запуска. Вы можете создать и применять общий класс [Оценщик](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) для отправки скрипта обучения с помощью любой удобной платформы машинного обучения (например, scikit-learn) с целью его дальнейшего запуска в любом целевом объекте вычислений — на локальном компьютере, отдельной виртуальной машине в Azure или в кластере GPU в Azure. Для заданий PyTorch, TensorFlow, Chainer и подкрепления в Машинном обучении Azure также предусмотрены соответствующие оценщики [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py), [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) и [Подкрепление](how-to-use-reinforcement-learning.md), которые облегчают использование этих платформ.

## <a name="train-with-an-estimator"></a>Обучение с оценщиком

После создания [рабочей области](concept-workspace.md) и настройки вашей [среды разработки](how-to-configure-environment.md) для обучения модели в Машинном обучении Azure необходимо выполнить следующие действия:  
1. Создайте [удаленный целевой объект вычислений](how-to-set-up-training-targets.md) (обратите внимание, что в качестве целевого объекта вычислений также можно использовать локальный компьютер).
2. Отправьте [данные для обучения](how-to-access-data.md) в хранилище данных (необязательно).
3. создать [сценарий обучения](tutorial-train-models-with-aml.md#create-a-training-script);
4. создать объект `Estimator`;
5. Отправьте класс оценщика в объект эксперимента в рабочей области.

В этой статье основное внимание уделено шагам 4–5. Примеры для шагов 1–3 см. в статье [Руководство 1. Обучение модели классификации изображений с помощью службы машинного обучения Azure](tutorial-train-models-with-aml.md).

### <a name="single-node-training"></a>Одноузловое обучение

Используйте `Estimator` для одноузлового тренировочного запуска в удаленной вычислительной среде в Azure для модели scikit-learn. У вас уже должен быть создан объект [целевого вычислительного ресурса](how-to-set-up-training-targets.md#amlcompute) `compute_target`и объект [FileDataset](how-to-create-register-datasets.md)`ds`.

```Python
from azureml.train.estimator import Estimator

script_params = {
    # to mount files referenced by mnist dataset
    '--data-folder': ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

Этот фрагмент кода определяет следующие параметры конструктора `Estimator`.

Параметр | Описание
--|--
`source_directory`| Локальный каталог, который содержит весь код, необходимый для задания обучения. Эта папка копируется с локального компьютера на удаленный вычислительный ресурс.
`script_params`| Словарь, указывающий аргументы командной строки для передачи в сценарий обучения `entry_script` в виде пар `<command-line argument, value>`. Чтобы задать флаг подробных сведений в `script_params`, используйте `<command-line argument, "">`.
`compute_target`| Удаленный целевой объект вычислений, на котором будет выполняться скрипт обучения. В нашем случае это кластер Вычислительной среды Машинного обучения Azure ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)). (Обратите внимание: даже если кластер AmlCompute чаще всего используется как целевой объект, вы можете выбрать другие типы целевых объектов вычислений, такие как виртуальные машины Azure или даже локальный компьютер.)
`entry_script`| Путь к файлу (относительно `source_directory`) сценария обучения, который будет выполняться на удаленном вычислительном ресурсе. В этой папке должны быть расположены этот файл и дополнительные файлы, от которых он зависит.
`conda_packages`| Необходимый для сценария обучения список пакетов Python, которые нужно установить с помощью conda.  

У конструктора есть другой параметр с именем `pip_packages`. Его можно использовать для всех необходимых пакетов pip.

Теперь, когда вы создали объект `Estimator`, можно выполнить задание обучения в удаленной вычислительной среде посредством вызова функции `submit` в объекте `experiment`[Эксперимента](concept-azure-machine-learning-architecture.md#experiments). 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Особые папки**. Две папки — *выходные данные* и *журналы*, обрабатываются Машинным обучением Azure специальным образом. Если вы записываете файлы в папки с именами *выходные данные* и *журналы*, которые относятся к корневому каталогу (`./outputs` и `./logs` соответственно), то эти файлы во время обучения автоматически будут отправляться в журнал выполнения, чтобы после завершения выполнения у вас был к ним доступ.
>
> Чтобы создать артефакты во время обучения (например, к файлам моделей, контрольным точкам, файлам данных или графическим изображениям), запишите их в папку `./outputs`.
>
> Аналогичным образом можно записать любые журналы выполнения обучения в папку `./logs`. Чтобы использовать [интеграцию TensorBoard](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) Машинного обучения Azure, убедитесь, что вы записываете журналы TensorBoard в эту папку. Во время выполнения вы сможете запустить TensorBoard и выполнить потоковую передачу этих журналов.  Позже вы также сможете восстановить журналы из любого из ваших предыдущих запусков.
>
> Например, чтобы загрузить файл, записанный в папку *выходные данные* локального компьютера, после запуска удаленного обучения выполните такую команду: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Распределенное обучение и настройка образов Docker

Существуют два сценария дополнительного обучения, которые вы можете выполнить с помощью `Estimator`:
* использование пользовательского образа Docker;
* распределенное обучение на кластере с несколькими узлами.

Ниже показан код, позволяющий выполнить распределенное обучение для модели Keras. Кроме того, предполагается, что для обучения вместо стандартных образов Машинного обучения Azure используется пользовательский образ Docker из контейнера Docker Hub `continuumio/miniconda`.

У вас уже должен быть создан объект `compute_target`[целевого вычислительного ресурса](how-to-set-up-training-targets.md#amlcompute). Вы можете создать оценщик следующим образом.

```Python
from azureml.train.estimator import Estimator
from azureml.core.runconfig import MpiConfiguration

estimator = Estimator(source_directory='./my-keras-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),        
                      conda_packages=['tensorflow', 'keras'],
                      custom_docker_image='continuumio/miniconda')
```

В приведенном выше коде показаны следующие новые параметры конструктора `Estimator`:

Параметр | Описание | По умолчанию
--|--|--
`custom_docker_image`| Имя используемого образа. Можно предоставлять только те образы, которые доступны в публичных хранилищах Docker (в данном случае в центре Docker). Чтобы выбрать образ из частного репозитория Docker, используйте параметр конструктора `environment_definition`. [Ознакомьтесь с примером ниже](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Количество узлов, которые будут использоваться для задания обучения. | `1`
`process_count_per_node`| Количество процессов (или рабочих ролей), запускаемых на каждом узле. В этом случае используется `2` графических процессора, доступных на каждом узле.| `1`
`distributed_training`| Объект [MPIConfiguration ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) для запуска распределенного обучения с помощью внутреннего сервера MPI.  | `None`


И наконец, отправьте задание обучения, выполнив такую команду.
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>регистрация модели;

После обучения модель можно сохранить и зарегистрировать в рабочей области. Регистрация модели позволяет хранить и редактировать версии моделей в рабочей области для упрощения [развертывания и управления моделями](concept-model-management-and-deployment.md).

Выполнение следующего кода приведет к регистрации модели в рабочей области и сделает ее доступной для ссылки по имени в удаленных контекстах вычислений или сценариях развертывания. Дополнительные сведения и дополнительные параметры см. в справочной документации в разделе [`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-).

```python
model = run.register_model(model_name='sklearn-sample', model_path=None)
```

## <a name="github-tracking-and-integration"></a>Отслеживание и интеграция GitHub

При выполнении запуска обучения, в котором исходный каталог является локальным репозиторием Git, сведения о репозитории хранятся в журнале выполнения. Дополнительные сведения см. в статье [Интеграция с Git для Машинного обучения Azure](concept-train-model-git-integration.md).

## <a name="examples"></a>Примеры
Записная книжка с основным сведениями о шаблоне оценщика:
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

Записная книжка для обучения модели scikit-learn с помощью оценщика:
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

Записные книжки для обучения моделей с помощью специальных оценщиков платформы глубокого обучения:

* [how-to-use-azureml/ml-frameworks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Отслеживание метрик выполнения во время обучения](how-to-track-experiments.md)
* [Обучение моделей PyTorch](how-to-train-pytorch.md)
* [Обучение моделей TensorFlow](how-to-train-tensorflow.md)
* [Обучение глубокой нейронной сети для обучения с подкреплением](how-to-use-reinforcement-learning.md)
* [Настройка гиперпараметров](how-to-tune-hyperparameters.md)
* [Развертывание обученной модели](how-to-deploy-and-where.md)
* [Создание сред для обучения и развертывания и управление ими](how-to-use-environments.md)