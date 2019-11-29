---
title: Обучение моделей машинного обучения с использованием средства оценки
titleSuffix: Azure Machine Learning
description: Узнайте, как выполнять централизованное и распределенное обучение традиционных моделей машинного обучения и модели глубокого обучения с помощью класса оценщик Машинное обучение Azure
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: 9bb22a564f52dfcdb3fbec6d842e452ca416059f
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961698"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Обучение моделей с помощью оценщика Машинного обучения Azure
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

С помощью Машинное обучение Azure можно легко отправить обучающий сценарий в [различные целевые объекты вычислений](how-to-set-up-training-targets.md#compute-targets-for-training), используя [объект RunConfiguration](how-to-set-up-training-targets.md#whats-a-run-configuration) и [объект скриптрунконфиг](how-to-set-up-training-targets.md#submit). Этот шаблон обеспечивает высокую гибкость и максимальный контроль.

Чтобы облегчить работу с моделью глубокого обучения, в пакете SDK Машинного обучения Azure для Python предоставляется альтернативная высокоуровневая абстракция — класс оценщика, который позволяет пользователям легко создавать конфигурации запуска. Вы можете создать и использовать универсальный [оценщик](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) для отправки сценария обучения, используя любую выбранную платформу обучения (например, scikit-learning) на любом выбранном целевом объекте вычислений, будь то ваш локальный компьютер, одна виртуальная машина в Azure или кластер GPU в Azure. Для задач PyTorch, TensorFlow и Chain Машинное обучение Azure также предоставляет соответствующие средства оценки [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)и [формирователя цепочки](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) для упрощения использования этих платформ.

## <a name="train-with-an-estimator"></a>Обучение с оценщиком

После создания [рабочей области](concept-workspace.md) и настройки вашей [среды разработки](how-to-configure-environment.md) для обучения модели в Машинном обучении Azure необходимо выполнить следующие действия:  
1. Создайте [удаленный целевой объект вычислений](how-to-set-up-training-targets.md) (обратите внимание, что в качестве целевого объекта вычислений также можно использовать локальный компьютер).
2. Отправьте [данные для обучения](how-to-access-data.md) в хранилище данных (необязательно).
3. создать [сценарий обучения](tutorial-train-models-with-aml.md#create-a-training-script);
4. создать объект `Estimator`;
5. Отправьте класс оценщика в объект эксперимента в рабочей области.

В этой статье основное внимание уделено шагам 4–5. Примеры для шагов 1–3 см. в статье [Руководство 1. Обучение модели классификации изображений с помощью службы машинного обучения Azure](tutorial-train-models-with-aml.md).

### <a name="single-node-training"></a>Одноузловое обучение

Используйте `Estimator` для одноузлового тренировочного запуска в удаленной вычислительной среде в Azure для модели scikit-learn. У вас уже должен быть создан объект [ ](how-to-set-up-training-targets.md#amlcompute)целевого вычислительного ресурса`compute_target` и объект [ ](how-to-access-data.md)хранилища данных`ds`.

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

Параметр | ОПИСАНИЕ
--|--
`source_directory`| Локальный каталог, который содержит весь код, необходимый для задания обучения. Эта папка копируется с локального компьютера на удаленное вычисление.
`script_params`| Словарь, указывающий аргументы командной строки для передачи в сценарий обучения `entry_script`в виде пар `<command-line argument, value>`. Чтобы задать флаг verbose в `script_params`, используйте `<command-line argument, "">`.
`compute_target`| Удаленный целевой объект вычислений, на котором будет выполняться скрипт обучения. В нашем случае это кластер Вычислительной среды Машинного обучения Azure ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)). (Примечание. Несмотря на то, что кластер Амлкомпуте является часто используемым целевым объектом, можно также выбрать другие типы целевых объектов вычислений, например виртуальные машины Azure или даже локальный компьютер.)
`entry_script`| Путь к файлу (относительно `source_directory`) сценария обучения, который будет выполняться на удаленном вычислительном ресурсе. Этот файл и все дополнительные файлы, от которых он зависит, должны находиться в этой папке.
`conda_packages`| Необходимый для сценария обучения список пакетов Python, которые нужно установить с помощью conda.  

Конструктор имеет другой параметр с именем `pip_packages`, который используется для любых требуемых пакетов PIP.

Теперь, когда вы создали объект `Estimator`, можно выполнить задание обучения в удаленной вычислительной среде посредством вызова функции `submit` в объекте [ ](concept-azure-machine-learning-architecture.md#experiments)Эксперимента`experiment`. 

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

У вас уже должен быть создан объект [ ](how-to-set-up-training-targets.md#amlcompute)целевого вычислительного ресурса`compute_target`. Вы можете создать оценщик следующим образом.

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

Параметр | ОПИСАНИЕ | значение по умолчанию
--|--|--
`custom_docker_image`| Имя используемого образа. Можно предоставлять только те образы, которые доступны в публичных хранилищах Docker (в данном случае в центре Docker). Чтобы выбрать образ из частного репозитория Docker, используйте параметр конструктора `environment_definition`. [Ознакомьтесь с примером ниже](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Количество узлов, которые будут использоваться для задания обучения. | `1`
`process_count_per_node`| Количество процессов (или рабочих ролей), запускаемых на каждом узле. В этом случае используется `2` графических процессора, доступных на каждом узле.| `1`
`distributed_training`| Объект [мпиконфигуратион](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) для запуска распределенного обучения с помощью внутреннего сервера MPI.  | `None`


И наконец, отправьте задание обучения, выполнив такую команду.
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="github-tracking-and-integration"></a>Отслеживание и интеграция GitHub

При запуске обучающего запуска, в котором исходный каталог является локальным репозиторием Git, сведения о репозитории хранятся в журнале выполнения. Дополнительные сведения см. в статье [Интеграция Git для машинное обучение Azure](concept-train-model-git-integration.md).

## <a name="examples"></a>Примеры
Для записной книжки, в которой показаны основные сведения о шаблоне средства оценки, см.:
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

Для записной книжки, которая обучает модель scikit-учиться с помощью средства оценки, см.:
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

Для записных книжек по обучающим моделям, использующих средства оценки, относящиеся к инфраструктуре глубокого обучения, см.:

* [Использование-azureml/ML-Frameworks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Дополнительная информация

* [Отслеживание метрик выполнения во время обучения](how-to-track-experiments.md)
* [Обучение моделей PyTorch](how-to-train-pytorch.md)
* [Обучение моделей TensorFlow](how-to-train-tensorflow.md)
* [Настройка гиперпараметров](how-to-tune-hyperparameters.md)
* [Развертывание обученной модели](how-to-deploy-and-where.md)
