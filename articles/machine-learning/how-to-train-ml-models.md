---
title: Обучение моделей машинного обучения с использованием средства оценки
titleSuffix: Azure Machine Learning
description: Узнайте, как выполнять одноузловое и распределенное обучение традиционным моделям машинного обучения и глубокого обучения с помощью класса Azure Machine Learning Estimator
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: 678af1855baf52efa727444236de8a1724a7d0b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79078478"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Обучение моделей с помощью оценщика Машинного обучения Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

С помощью Машинного обучения Azure вы можете легко отправлять свой обучаемый скрипт [различным вычислительным целям,](how-to-set-up-training-targets.md#compute-targets-for-training)используя [объект RunConfiguration](how-to-set-up-training-targets.md#whats-a-run-configuration) и [объект ScriptRunConfig.](how-to-set-up-training-targets.md#submit) Этот шаблон обеспечивает высокую гибкость и максимальный контроль.

Чтобы облегчить работу с моделью глубокого обучения, в пакете SDK Машинного обучения Azure для Python предоставляется альтернативная высокоуровневая абстракция — класс оценщика, который позволяет пользователям легко создавать конфигурации запуска. Можно создать и использовать общий [оценщик](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) для отправки обучаемого скрипта с использованием выбранной вами инфраструктуры обучения (например, scikit-learn) на любой выбранной вами вычислительной цели, будь то локальная машина, единый VM в Azure или кластер графического процессора в Azure. Для задач PyTorch, TensorFlow и Chainer Azure Machine Learning также предоставляет соответствующие оценки [PyTorch,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)и [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) для упрощения использования этих инфраструктур.

## <a name="train-with-an-estimator"></a>Обучение с оценщиком

После создания [рабочей области](concept-workspace.md) и настройки вашей [среды разработки](how-to-configure-environment.md) для обучения модели в Машинном обучении Azure необходимо выполнить следующие действия:  
1. Создайте [удаленный целевой объект вычислений](how-to-set-up-training-targets.md) (обратите внимание, что в качестве целевого объекта вычислений также можно использовать локальный компьютер).
2. Отправьте [данные для обучения](how-to-access-data.md) в хранилище данных (необязательно).
3. Создайте [сценарий обучения](tutorial-train-models-with-aml.md#create-a-training-script)
4. создать объект `Estimator`;
5. Отправьте класс оценщика в объект эксперимента в рабочей области.

В этой статье основное внимание уделено шагам 4–5. Примеры для шагов 1–3 см. в статье [Руководство 1. Обучение модели классификации изображений с помощью службы машинного обучения Azure](tutorial-train-models-with-aml.md).

### <a name="single-node-training"></a>Одноузловое обучение

Используйте `Estimator` для одноузлового тренировочного запуска в удаленной вычислительной среде в Azure для модели scikit-learn. Вы должны были уже создать `compute_target` [объект вычислительной техники](how-to-set-up-training-targets.md#amlcompute) и объект `ds` [FileDataset.](how-to-create-register-datasets.md)

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
`source_directory`| Локальный каталог, который содержит весь код, необходимый для задания обучения. Эта папка скопируется с локальной машины на удаленный компьютер.
`script_params`| Словарь с указанием аргументов командной строки `entry_script`для передачи `<command-line argument, value>` на ваш обучающий сценарий в виде пар. Чтобы указать многословный `script_params`флаг `<command-line argument, "">`в, используйте .
`compute_target`| Дистанционная вычислительная цель, на которую будет работать ваш обучаемый скрипт, в данном случае кластер Azure Machine Learning Compute[(AmlCompute).](how-to-set-up-training-targets.md#amlcompute) (Обратите внимание, что кластер AmlCompute является обычно используемой целью, можно также выбрать другие типы вычислительных целей, такие как Azure VMs или даже локальный компьютер.)
`entry_script`| Путь к файлу (относительно `source_directory`) сценария обучения, который будет выполняться на удаленном вычислительном ресурсе. Этот файл и все дополнительные файлы, от которых он зависит, должны быть расположены в этой папке.
`conda_packages`| Необходимый для сценария обучения список пакетов Python, которые нужно установить с помощью conda.  

Конструктор имеет другой параметр, называемый, `pip_packages` который вы используете для любых необходимых пакетов пипсов.

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
> Аналогичным образом можно записать любые журналы выполнения обучения в папку `./logs`. Чтобы использовать [интеграцию TensorBoard](https://aka.ms/aml-notebook-tb) Машинного обучения Azure, убедитесь, что вы записываете журналы TensorBoard в эту папку. Во время выполнения вы сможете запустить TensorBoard и выполнить потоковую передачу этих журналов.  Позже вы также сможете восстановить журналы из любого из ваших предыдущих запусков.
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

Параметр | Описание | Значение по умолчанию
--|--|--
`custom_docker_image`| Имя используемого образа. Можно предоставлять только те образы, которые доступны в публичных хранилищах Docker (в данном случае в центре Docker). Чтобы выбрать образ из частного репозитория Docker, используйте параметр конструктора `environment_definition`. [Ознакомьтесь с примером ниже](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Количество узлов, которые будут использоваться для задания обучения. | `1`
`process_count_per_node`| Количество процессов (или рабочих ролей), запускаемых на каждом узле. В этом случае используется `2` графических процессора, доступных на каждом узле.| `1`
`distributed_training`| [MpIConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) объект для запуска распределенного обучения с использованием MPI бэкэнд.  | `None`


И наконец, отправьте задание обучения, выполнив такую команду.
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>регистрация модели;

После того как вы обучили модель, вы можете сохранить и зарегистрировать его в рабочее пространство. Регистрация моделей позволяет хранить и отстраивать модели в рабочем пространстве для упрощения [управления и развертывания моделей.](concept-model-management-and-deployment.md)

Запуск следующего кода зарегистрирует модель в рабочем пространстве и сделает ее доступной для ссылки по имени в контекстах удаленных вычислений или сценариях развертывания. Дополнительную информацию и дополнительные параметры можно узнать [`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) в справочных документах.

```python
model = run.register_model(model_name='sklearn-sample', model_path=None)
```

## <a name="github-tracking-and-integration"></a>Отслеживание и интеграция GitHub

При запуске обучаемого запуска, в котором исходный каталог является локальным репозиторием Git, информация о репозитории хранится в истории выполнения. Для получения дополнительной информации см. [интеграция Git для машинного обучения Azure.](concept-train-model-git-integration.md)

## <a name="examples"></a>Примеры
Для ноутбука, который показывает основы шаблона оценщика, см.:
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

Для ноутбука, который тренирует scikit-узнать модель с помощью оценщика, см.:
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

Для тетрадей на обучающих моделях с помощью конкретных оценщиков глубокого обучения см.:

* [как-к-использованию-лазурит/мл-рамки](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Отслеживание метрик выполнения во время обучения](how-to-track-experiments.md)
* [Обучение моделей PyTorch](how-to-train-pytorch.md)
* [Обучение моделей TensorFlow](how-to-train-tensorflow.md)
* [Настройка гиперпараметров](how-to-tune-hyperparameters.md)
* [Развертывание обученной модели](how-to-deploy-and-where.md)
* [Создание и управление средами для обучения и развертывания](how-to-use-environments.md)