---
title: Обучение модели с помощью пользовательского образа DOCKER
titleSuffix: Azure Machine Learning
description: Узнайте, как обучить модели с пользовательскими образами DOCKER в Машинное обучение Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 08/11/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 2289a761d4e266c305c2868e9f234871624ae528
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661311"
---
# <a name="train-a-model-using-a-custom-docker-image"></a>Обучение модели с помощью пользовательского образа DOCKER
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Из этой статьи вы узнаете, как использовать пользовательский образ DOCKER при обучении моделей с Машинное обучение Azure. 

Примеры сценариев в этой статье используются для классификации образов Pet путем создания свертки нейронной сети. 

Хотя Машинное обучение Azure предоставляет базовый образ DOCKER по умолчанию, можно также использовать Машинное обучение Azure среды, чтобы указать конкретный базовый образ, например один из набора обслуживаемых [базовых образов машинного обучения Azure](https://github.com/Azure/AzureML-Containers) или собственного [пользовательского образа](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image#create-a-custom-base-image). Пользовательские базовые образы позволяют тесно управлять зависимостями и поддерживать более строгий контроль версий компонентов при выполнении заданий обучения. 

## <a name="prerequisites"></a>Предварительные требования 
Запустите этот код в любой из этих сред:
* Вычислительная операция Машинного обучения Azure — загрузка или установка не требуется
    * Выполните инструкции из [учебника Настройка среды и рабочей области](tutorial-1st-experiment-sdk-setup.md) , чтобы создать выделенный сервер записной книжки, предварительно загруженный с помощью пакета SDK и примера репозитория.
    * В [репозитории примеров](https://github.com/Azure/azureml-examples)машинное обучение Azure найдите готовую записную книжку, перейдя к этому каталогу: **записные книжки > фастаи > Train-pets-resnet34. ipynb** . 

* Собственный сервер Jupyter Notebook
    * Создайте [файл конфигурации рабочей области](how-to-configure-environment.md#workspace).
    * [Пакет SDK для Машинного обучения Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true). 
    * [Реестр контейнеров Azure](/azure/container-registry) или другой реестр DOCKER, доступный в Интернете.

## <a name="set-up-the-experiment"></a>Настройка эксперимента 
В этом разделе настраивается обучающий эксперимент путем инициализации рабочей области, создания эксперимента и передачи обучающих данных и сценариев обучения.

### <a name="initialize-a-workspace"></a>Инициализация рабочей области
[Машинное обучение Azure Рабочая область](concept-workspace.md) — это ресурс верхнего уровня для службы. Она предоставляет централизованное расположение для работы со всеми создаваемыми артефактами. В пакете SDK для Python можно получить доступ к артефактам рабочей области, создав [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) объект.

Создайте объект рабочей области из `config.json` файла, созданного в [разделе Предварительные требования](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>Подготовка скриптов
В этом руководстве описывается **Train.py** сценарий [обучения.](https://github.com/Azure/azureml-examples/blob/main/code/models/fastai/pets-resnet34/train.py) На практике вы можете использовать любой пользовательский сценарий обучения, как есть, и запустить его с помощью Машинное обучение Azure.

### <a name="define-your-environment"></a>Определение среды
Создайте объект среды и включите DOCKER. 

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

Этот указанный базовый образ поддерживает библиотеку fast.ai, которая позволяет использовать распределенные возможности глубокого обучения. Дополнительные сведения см. в разделе [fast.AI DockerHub](https://hub.docker.com/u/fastdotai). 

При использовании пользовательского образа DOCKER возможно, что среда Python уже настроена правильно. В этом случае установите `user_managed_dependencies` для флага значение true, чтобы использовать встроенную среду Python на основе пользовательского образа. По умолчанию служба машинного обучения Azure создает среду Conda с указанными зависимостями и выполняет запуск в этой среде вместо использования библиотек Python, установленных на базовом образе.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

Чтобы использовать образ из закрытого реестра контейнеров, который отсутствует в рабочей области, необходимо использовать `docker.base_image_registry` для указания адреса репозитория и имени пользователя и пароля:

```python
# Set the container registry information
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

Также можно использовать пользовательский Dockerfile. Используйте этот подход, если необходимо установить пакеты, отличные от Python, как зависимости, и не забудьте установить базовый образ в значение None.

```python 
# Specify docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

### <a name="create-or-attach-existing-amlcompute"></a>Создание или присоединение существующего Амлкомпуте
Вам потребуется создать [целевой объект вычислений](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#compute-target) для обучения модели. В этом руководстве вы создадите Амлкомпуте в качестве обучающего ресурса для учебных вычислений.

Создание Амлкомпуте занимает примерно 5 минут. Если Амлкомпуте с таким именем уже находится в рабочей области, этот код пропустит процесс создания.

Как и в случае с другими службами Azure, существуют ограничения на определенные ресурсы (например, Амлкомпуте), связанные со службой Машинное обучение Azure. Ознакомьтесь с ограничениями по умолчанию [и сведениями](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas) о том, как запросить дополнительную квоту. 

```python
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

# use get_status() to get a detailed status for the current AmlCompute
print(compute_target.get_status().serialize())
```

### <a name="create-a-scriptrunconfig"></a>Создание Скриптрунконфиг
Эта Скриптрунконфиг настроит задание для выполнения на нужном [целевом объекте вычислений](https://docs.microsoft.com/azure/machine-learning/how-to-set-up-training-targets#compute-targets-for-training).

```python
from azureml.core import ScriptRunConfig

fastai_config = ScriptRunConfig(source_directory='fastai-example', script='train.py')
fastai_config.run_config.environment = fastai_env
fastai_config.run_config.target = compute_target
```

### <a name="submit-your-run"></a>Отправка выполнения
При отправке обучающего запуска с помощью объекта Скриптрунконфиг метод Submit возвращает объект типа Скриптрун. Возвращенный объект Скриптрун предоставляет программный доступ к сведениям о ходе выполнения обучения. 

```python
from azureml.core import Experiment

run = Experiment(ws,'fastai-custom-image').submit(fastai_config)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Машинное обучение Azure запускает скрипты обучения, копируя весь исходный каталог. Если у вас есть конфиденциальные данные, которые не нужно передавать, используйте [файл. Ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) или не включайте его в исходный каталог. Вместо этого получите доступ к данным с помощью [хранилища](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py&preserve-view=true)данных.

Дополнительные сведения о настройке среды Python см. в статье [создание & использование программных сред](how-to-use-environments.md). 

## <a name="next-steps"></a>Дальнейшие действия
В этой статье вы обучили модель с помощью пользовательского образа DOCKER. Дополнительные сведения о Машинное обучение Azure см. в других статьях.
* [Мониторинг метрик выполнения](how-to-track-experiments.md) во время обучения
* [Развертывание модели](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image) с помощью пользовательского образа DOCKER.
