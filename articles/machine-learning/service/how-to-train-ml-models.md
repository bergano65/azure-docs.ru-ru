---
title: Обучение моделей машинного обучения с использованием средства оценки
titleSuffix: Azure Machine Learning service
description: Узнайте, как выполнять одноузловое и распределенное обучение традиционного машинного обучения и моделей глубокого обучения с помощью класса Estimator Служб машинного обучения Azure
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 005854a51916d36bbad56f1296f17fa687020359
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251407"
---
# <a name="train-models-with-azure-machine-learning"></a>Обучение моделей с помощью Машинного обучения Azure

Для обучения моделей машинного обучения, в особенности глубоких нейронных сетей, часто требуется много времени и вычислений. После того как вы закончите писать сценарий обучения и работать с небольшим подмножеством данных на вашем локальном компьютере вы, вероятно, захотите увеличить свою рабочую нагрузку.

Чтобы облегчить обучение, пакет SDK Машинного обучения Azure для Python обеспечивает высокоуровневую абстракцию класса Estimator, которая позволяет пользователям легко обучать свои модели в экосистеме Azure. Вы можете создать и использовать объект [`Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py)для отправки любого обучающего кода, который необходимо запустить на удаленном компьютере, будь то запуск на отдельном узле или распределенное обучение на кластере с GPU. Для заданий PyTorch и TensorFlow Машинное обучение Azure также предоставляет соответствующие пользовательские оценщики `PyTorch` и `TensorFlow`, которые упрощают использование этих платформ.

## <a name="train-with-an-estimator"></a>Обучение с оценщиком

После создания [рабочей области](concept-azure-machine-learning-architecture.md#workspace) и настройки вашей [среды разработки](how-to-configure-environment.md) для обучения модели в Машинном обучении Azure необходимо выполнить следующие действия:  
1. создать [удаленный целевой объект вычисления](how-to-set-up-training-targets.md);
2. отправить [данные для обучения](how-to-access-data.md) (необязательно);
3. создать [сценарий обучения](tutorial-train-models-with-aml.md#create-a-training-script);
4. создать объект `Estimator`;
5. Отправка задания обучения

В этой статье основное внимание уделено шагам 4–5. Примеры для шагов 1–3 см. в статье [Руководство 1. Обучение модели классификации изображений с помощью службы машинного обучения Azure](tutorial-train-models-with-aml.md).

### <a name="single-node-training"></a>Одноузловое обучение

Используйте `Estimator` для одноузлового тренировочного запуска в удаленной вычислительной среде в Azure для модели scikit-learn. У вас уже должен быть создан объект `compute_target` [целевого вычислительного ресурса](how-to-set-up-training-targets.md#amlcompute) и объект `ds` [хранилища данных](how-to-access-data.md).

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
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
`source_directory`| Локальный каталог, который содержит весь код, необходимый для задания обучения. Эта папка копируется с локального компьютера на удаленный вычислительный ресурс. 
`script_params`| Словарь, указывающий аргументы командной строки для сценария обучения `entry_script` в виде пар <аргумент командной строки, значение>.
`compute_target`| Удаленный целевой объект вычислений, на котором будет выполняться сценарий обучения. В нашем случае это кластер Вычислительной среды Машинного обучения Azure ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)).
`entry_script`| Путь к файлу (относительно `source_directory`) сценария обучения, который будет выполняться на удаленном вычислительном ресурсе. В этой папке должны быть расположены этот файл и дополнительные файлы, от которых он зависит.
`conda_packages`| Необходимый для сценария обучения список пакетов Python, которые нужно установить с помощью conda.  
У конструктора есть другой параметр с именем `pip_packages`. Его можно использовать для всех необходимых пакетов pip.

Теперь, когда вы создали объект `Estimator`, можно выполнить задание обучения в удаленной вычислительной среде посредством вызова функции `submit` в объекте `experiment` [Эксперимента](concept-azure-machine-learning-architecture.md#experiment). 

```Python
run = experiment.submit(sk_est)
print(run.get_details().status)
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

Следующий код показывает, как выполнить распределенное обучение для модели CNTK. Кроме того, вместо использования образов по умолчанию Машинного обучения Azure предполагается, что вы используете собственный пользовательский образ Docker для обучения.

У вас уже должен быть создан объект `compute_target` [целевого вычислительного ресурса](how-to-set-up-training-targets.md#amlcompute). Вы можете создать оценщик следующим образом.

```Python
from azureml.train.estimator import Estimator

estimator = Estimator(source_directory='./my-cntk-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',     
                      pip_packages=['cntk==2.5.1'],
                      custom_docker_base_image='microsoft/mmlspark:0.12')
```

В приведенном выше коде показаны следующие новые параметры конструктора `Estimator`:

Параметр | ОПИСАНИЕ | значение по умолчанию
--|--|--
`custom_docker_base_image`| Имя используемого образа. Можно предоставлять только те образы, которые доступны в публичных хранилищах Docker (в данном случае в центре Docker). Чтобы использовать образ из репозитория Docker, используйте параметр конструктора `environment_definition`. | `None`
`node_count`| Количество узлов, которые будут использоваться для задания обучения. | `1`
`process_count_per_node`| Количество процессов (или рабочих ролей), запускаемых на каждом узле. В этом случае используется `2` графических процессора, доступных на каждом узле.| `1`
`distributed_backend`| Серверная часть для запуска распределенного обучения, предлагаемая средством оценки с помощью MPI.  Чтобы выполнять параллельное или распределенное обучение (например, `node_count`> 1 ,или `process_count_per_node`> 1, или оба варианта), задайте `distributed_backend='mpi'`. Реализация MPI, используемая AML — [Open MPI](https://www.open-mpi.org/).| `None`

И наконец, отправьте задание обучения, выполнив такую команду.
```Python
run = experiment.submit(cntk_est)
```

## <a name="examples"></a>Примеры
Записная книжка, которая обучает модель sklearn:
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

Записные книжки для распределенного глубокого обучения:
* [how-to-use-azureml/training-with-deep-learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Дополнительная информация

* [Отслеживание метрик выполнения во время обучения](how-to-track-experiments.md)
* [Обучение моделей PyTorch](how-to-train-pytorch.md)
* [Обучение моделей TensorFlow](how-to-train-tensorflow.md)
* [Настройка гиперпараметров](how-to-tune-hyperparameters.md)
* [Развертывание обученной модели](how-to-deploy-and-where.md)
