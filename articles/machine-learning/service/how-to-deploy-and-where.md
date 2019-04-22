---
title: Как и способа развертывания модели
titleSuffix: Azure Machine Learning service
description: 'Сведения о том, как и где следует развертывать модели Службы машинного обучения Azure, в том числе: Экземпляры контейнеров Azure, Службу Azure Kubernetes, Azure IoT Edge и программируемые пользователем вентильные матрицы.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 04/02/2019
ms.custom: seoapril2019
ms.openlocfilehash: a6ef53d56fa293791658b37b16cbaff94aee6ef3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59280899"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Развертывание моделей с помощью Службы машинного обучения Azure

В рамках этого документа вы научитесь развертывать модель как веб-службу в облаке Azure и на устройствах IoT Edge. 

## <a name="compute-targets-for-deployment"></a>Целевых объектов вычислений для развертывания

Использование пакета SDK для Azure Machine Learning развертывание обученной модели в следующие расположения:

| Целевой объект вычисления | Тип развертывания | ОПИСАНИЕ |
| ----- | ----- | ----- |
| [Служба Azure Kubernetes (AKS)](#aks) | Вывод в режиме реального времени | Подходит для крупномасштабных рабочих развертываний. Она обеспечивает автоматическое масштабирование и малое время отклика. |
| [Azure вычислений для машинного обучения (amlcompute)](#azuremlcompute) | Определение пакета | Запустите пакетный прогноз на бессерверных вычислений. Поддерживает нормальные и низкоприоритетные виртуальные машины. |
| [Экземпляры контейнеров Azure (ACI)](#aci) | Тестирование | Идеальны для разработки и (или) тестирования. **Не подходит для рабочих нагрузок.** |
| [Edge Интернета вещей Azure](#iotedge) | (Предварительная версия) Модуль Интернета вещей | Развертывает модели на устройствах Интернета вещей. Формирование выводов происходит на устройстве. |
| [Программируемая пользователем вентильная матрица(ППВМ).](#fpga) | (Предварительная версия) Веб-службы | Сверхнизкая задержка для формирования выводов в режиме реального времени. |

## <a name="deployment-workflow"></a>Рабочий процесс развертывания

Процесс развертывания модели аналогичен для всех целевых объектов вычислений:

1. Обучение и регистрация модели.
1. Настройка и регистрация образа, использующего модель.
1. Развертывание образа в целевом объекте вычислений.
1. тестирование развертывания

В следующем видеоролике показано развертывание экземпляры контейнеров Azure:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Kwk3]


Дополнительные сведения об основных понятиях, связанных с рабочим процессом развертывания, см. в статье [Администрирование, развертывание и мониторинг моделей с помощью службы "Машинное обучение Azure"](concept-model-management-and-deployment.md).

## <a name="prerequisites-for-deployment"></a>Необходимые условия для развертывания

[!INCLUDE [aml-prereq](../../../includes/aml-prereq.md)]

- Обученная модель. Если у вас нет обученной модели, обучите и зарегистрируйте ее в Службе машинного обучения Azure в соответствии с инструкциями в учебнике [Обучение моделей](tutorial-train-models-with-aml.md).

    > [!NOTE]
    > Хотя службы машинного обучения Azure можно работать с любой общей моделью, могут загружаться в Python 3, в примерах в этом документе показано, с помощью модели, хранящиеся в формате Python pickle.
    >
    > Дополнительные сведения об их использовании см. в статье [ONNX и машинное обучение Azure: создание и развертывание совместимых моделей AI](how-to-build-deploy-onnx.md).

## <a id="registermodel"></a> Регистрация обученной модели

Реестр моделей позволяет хранить и упорядочивать обученные модели в облаке Azure. Модели зарегистрированы в рабочей области Службы машинного обучения Azure. Модель можно обучить с помощью Машинного обучения Azure или другой службы. Следующий код демонстрирует регистрации модели из файла, задайте имя, описание и теги:

```python
from azureml.core.model import Model

model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)
```

**Оценка времени**. Примерно 10 секунд.

Пример регистрации модели, см. в разделе [обучить классификатор изображений](tutorial-train-models-with-aml.md).

Дополнительные сведения см. в справочной документации по [классу Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a id="configureimage"></a> Создание и регистрация образа

Развернутые модели упаковываются в виде образа. Образ содержит зависимости, необходимые для запуска модели.

Для создания конфигурации образа развертываний **экземпляра контейнера Azure**, **Службы Azure Kubernetes** и **Azure IoT Edge** используется класс [azureml.core.image.ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py). Затем конфигурация образа используется для создания образа Docker.

При создании конфигурации образа можно использовать либо __изображение по умолчанию__ предоставляемых службой машинного обучения Azure или __пользовательский образ__ вами.

В приведенном ниже коде показано, как создать новую конфигурацию образа.

```python
from azureml.core.image import ContainerImage

# Image configuration
image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                 runtime = "python",
                                                 conda_file = "myenv.yml"}
                                                 )
```

**Оценка времени**. Примерно 10 секунд.

В следующей таблице описаны важные параметры, используемые в этом примере:

| Параметр | ОПИСАНИЕ |
| ----- | ----- |
| `execution_script` | Указывает сценарий Python, который используется для получения запросов, отправленных в службу. В этом примере сценарий содержится в файле `score.py`. Дополнительные сведения см. в разделе [Сценарий выполнения](#script). |
| `runtime` | Указывает, что образ использует Python. Другой вариант — `spark-py`, который использует Python с Apache Spark. |
| `conda_file` | Используется для предоставления файла среды Conda. Этот файл определяет среду Conda для развернутой модели. Дополнительные сведения о создании этого файла см. в разделе [Создание файла среды (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file). |

Пример создания конфигурация образа, см. в разделе [развертывание классификатора изображений](tutorial-deploy-models-with-aml.md).

Дополнительные сведения см. в справочной документации по [классу ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py).

### <a id="customimage"></a> Использование пользовательского образа

При использовании пользовательского образа, этот образ должен соответствовать следующим требованиям:

* Ubuntu 16.04 или более поздней версии.
* Conda 4.5. # или более поздней версии.
* Python 3.5. # или 3.6. #.

Чтобы использовать пользовательский образ, задайте `base_image` свойство конфигурации образа на адрес образа. Следующий пример демонстрирует использование образа из обоих открытого и закрытого реестра контейнеров Azure:

```python
# use an image available in public Container Registry without authentication
image_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"

# or, use an image available in a private Container Registry
image_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
image_config.base_image_registry.address = "myregistry.azurecr.io"
image_config.base_image_registry.username = "username"
image_config.base_image_registry.password = "password"
```

Дополнительные сведения об отправке образов в реестр контейнеров Azure, см. в разделе [отправка первого образа в частный реестр контейнеров Docker](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli).

Если ваша модель обучается на вычислений машинного обучения Azure, с помощью __версии 1.0.22 или более поздней версии__ пакета SDK для обучения машины Azure, образ создается во время обучения. Следующий пример демонстрирует использование этого образа:

```python
# Use an image built during training with SDK 1.0.22 or greater
image_config.base_image = run.properties["AzureML.DerivedImageName"]
```

### <a id="script"></a> Сценарий выполнения

Сценарий выполнения получает данные, отправленные в развернутый образ, и передает их в модель. Затем он принимает ответ, возвращенный моделью, и возвращает его клиенту. **Сценарий относится только к модели**; его необходимо понимать, модель ожидает и возвращает данные. Пример сценария, который работает с моделью классификации изображений, см. в разделе [развертывание классификатора изображений](tutorial-deploy-models-with-aml.md).

Сценарий содержит две функции, которые загружают и запустить модель:

* `init()`: Обычно эта функция загружает модель в глобальный объект. Эта функция выполняется только один раз при запуске контейнера Docker.

* `run(input_data)`: Эта функция использует модель для прогнозирования значения на основе входных данных. Ко входным и выходным данным для запуска обычно применяется формат JSON для сериализации и десериализации. Вы также можете работать с необработанными двоичными данными. Вы можете преобразовать данные, прежде чем отправлять их в модель или возвращать клиенту.

#### <a name="working-with-json-data"></a>Работа с данными JSON

Следующий пример сценария принимает и возвращает данные JSON. Функция `run` преобразует данные из JSON в формат, ожидаемый моделью, а затем преобразует ответ JSON перед его возвратом:

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression
from azureml.core.model import Model

# load the model
def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

# Passes data to the model and returns the prediction
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

#### <a name="working-with-binary-data"></a>Работа с двоичными данными

Если ваша модель принимает __двоичные данные__, используйте `AMLRequest`, `AMLResponse` и `rawhttp`. Следующий пример сценария принимает двоичные данные и возвращает обращенные байты для запросов POST. Для запросов GET он возвращает полный URL-адрес в тексте ответа:

```python
from azureml.contrib.services.aml_request  import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

# Accept and return binary data
@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    # handle GET requests
    if request.method == 'GET':
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    # handle POST requests
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        respBody = bytearray(reqBody)
        respBody.reverse()
        respBody = bytes(respBody)
        return AMLResponse(respBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> Пространство имен `azureml.contrib` часто изменяется, так как мы работаем над улучшением службы. Поэтому все, что доступно в этом пространстве имен, считается предварительными версиями компонентов, поддержка которых корпорацией Майкрософт ограничена.
>
> Если необходимо протестировать их в локальной среде разработки, можно установить эти компоненты в пространстве имен `contrib`, используя следующую команду:
> ```shell
> pip install azureml-contrib-services
> ```

### <a id="createimage"></a> Регистрация образа

После создания конфигурации образа ее можно использовать для регистрации образа. Этот образ хранится в реестре контейнеров рабочей области. После создания образа его можно развертывать для нескольких служб.

```python
# Register the image from the image configuration
image = ContainerImage.create(name = "myimage",
                              models = [model], #this is the model object
                              image_config = image_config,
                              workspace = ws
                              )
```

**Примерное время**: приблизительно 3 минуты.

При регистрации нескольких образов с тем же именем версии образов присваиваются автоматически. Например, первому образу, зарегистрированному как `myimage`, присваивается идентификатор `myimage:1`. При следующей регистрации образа с именем `myimage` идентификатором нового образа будет `myimage:2`.

Дополнительные сведения см. в справочной документации по [классу ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py).

## <a id="deploy"></a> Развертывание веб-службы

На этапе развертывания процесс немного отличается в зависимости от целевого объекта вычислений, на котором оно выполняется. Из следующих разделов вы узнаете, как развернуть:

| Целевой объект вычисления | Тип развертывания | ОПИСАНИЕ |
| ----- | ----- | ----- |
| [Служба Azure Kubernetes (AKS)](#aks) | Веб-службы (в режиме реального времени получение)| Подходит для крупномасштабных рабочих развертываний. Она обеспечивает автоматическое масштабирование и малое время отклика. |
| [Вычислений для машинного Обучения Azure](#azuremlcompute) | Веб-службы (получение пакетной службы)| Запустите пакетный прогноз на бессерверных вычислений. Поддерживает нормальные и низкоприоритетные виртуальные машины. |
| [Экземпляры контейнеров Azure (ACI)](#aci) | Веб-службы (разработки и тестирования)| Идеальны для разработки и (или) тестирования. **Не подходит для рабочих нагрузок.** |
| [Edge Интернета вещей Azure](#iotedge) | (Предварительная версия) Модуль Интернета вещей | Развертывает модели на устройствах Интернета вещей. Формирование выводов происходит на устройстве. |
| [Программируемая пользователем вентильная матрица(ППВМ).](#fpga) | (Предварительная версия) Веб-службы | Сверхнизкая задержка для формирования выводов в режиме реального времени. |

> [!IMPORTANT]
> Предоставление общего доступа к ресурсам независимо от источника (CORS) при развертывании модели в качестве веб-службы сейчас не поддерживается.

В примерах этого раздела используется [deploy_from_image](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-), который необходимо зарегистрировать модель и образ перед выполнением развертывания. Дополнительные сведения о методах развертывания см. в разделе [развертывание](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) и [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-).

### <a id="aci"></a> Развертывание в экземпляры контейнеров Azure (DEVTEST)

Экземпляры контейнеров Azure (ACI) можно использовать для развертывания моделей в качестве веб-службы, если выполняется одно или несколько из следующих условий:

- вам важно быстро выполнять развертывание и проверку модели. Развертывание в ACI занимает менее 5 минут;
- вы тестируете модель, которая находится в стадии разработки. Дополнительные сведения о квотах и доступности по регионам для службы "Экземпляры контейнеров Azure" см. в [этой статье](https://docs.microsoft.com/azure/container-instances/container-instances-quotas).

Для развертывания в службе "Экземпляры контейнеров Azure" следуйте приведенным ниже инструкциям.

1. Определите конфигурацию развертывания. Эта конфигурация зависит от требований вашей модели. В следующем примере определяется конфигурация c одним ядром ЦП и 1 ГБ памяти:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configAci)]

2. Для развертывания образа из раздела [Создание образа](#createimage) данного документа используйте следующий код:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3Deploy)]

    **Оценка времени**. Примерно через 5 минут.

Дополнительные сведения см. в справочной документации по классам [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) и [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

### <a id="aks"></a> Развертывание в службе Azure Kubernetes (рабочая СРЕДА)

Для развертывания модели в качестве крупномасштабной рабочей веб-службы используется Служба Azure Kubernetes (AKS). Можно использовать существующий кластер AKS или создать его с помощью пакета SDK для службы "Машинное обучение Azure", CLI или портала Azure.

Кластер AKS для рабочей области нужно создать только один раз. Один кластер можно использовать для нескольких развертываний.

> [!IMPORTANT]
> Если вы удаляете кластер, во время следующего развертывания нужно будет создать новый кластер.

Служба Azure Kubernetes предоставляет следующие возможности.

* Автомасштабирование
* Ведение журналов
* Сбор данных модели
* Малое время отклика для веб-служб
* Обработку подключений TLS
* Authentication

#### <a name="autoscaling"></a>Автомасштабирование

Автоматическое масштабирование можно управлять, задав `autoscale_target_utilization`, `autoscale_min_replicas`, и `autoscale_max_replicas` для AKS веб-службы. Следующий пример демонстрирует Включение автоматического масштабирования:

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True,
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

Решения для масштабирования вверх/вниз создан на основе использования из текущего контейнера реплик. Число реплик, которые заняты (обработки запроса), разделенный на общее число текущих реплик является текущий уровень использования. Если это число превышает целевое использование, то будут созданы несколько реплик. Если ниже, сокращаются реплик. По умолчанию целевое использование равно 70%.

Решения, позволяющие добавлять реплики создана и быстро реализовать (примерно 1 секунда). Решения для удаления реплики требуется больше времени (около 1 минута). Это поведение хранит простоя реплики вокруг в течение минуты, на случай поступления новых запросов, что они могут обрабатывать.

Требуется реплики можно вычислить с помощью следующего кода:

```python
from math import ceil
# target requests per second
targetRps = 20
# time to process the request (in seconds)
reqTime = 10
# Maximum requests per container
maxReqPerContainer = 1
# target_utilization. 70% in this example
targetUtilization = .7

concurrentRequests = targetRps * reqTime / targetUtilization

# Number of container replicas
replicas = ceil(concurrentRequests / maxReqPerContainer)
```

Дополнительные сведения о параметр `autoscale_target_utilization`, `autoscale_max_replicas`, и `autoscale_min_replicas`, см. в разделе [AksWebservice.deploy_configuration](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none-) ссылки.

#### <a name="create-a-new-cluster"></a>Создание кластера:

Чтобы создать кластер Службы Azure Kubernetes, используйте следующий код:

> [!IMPORTANT]
> Кластер AKS для рабочей области нужно создать только один раз. Созданный кластер можно использовать для различных развертываний. Если вы удаляете кластер или группу ресурсов, в которую он входит, во время следующего развертывания нужно будет создать новый кластер.
> Если вы выберете пользовательские значения agent_count и vm_size для [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), необходимо убедиться, что произведение agent_count и vm_size больше или равно 12 виртуальным ЦП. Например, если вы используете для vm_size значение "Standard_D3_v2", которое соответствует 4 виртуальным ЦП, для agent_count необходимо выбрать значение 3 или больше.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

**Примерное время**: приблизительно 20 минут.

#### <a name="use-an-existing-cluster"></a>Использование имеющегося кластера

Если вас уже есть кластер AKS в подписке Azure, и это версии 1.12. ## и имеет по крайней мере 12 виртуальных ЦП, его можно использовать для развертывания образа. Приведенный ниже показано, как подключить существующий 1.12 AKS. ## кластера к рабочей области:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)

# Wait for the operation to complete
aks_target.wait_for_completion(True)
```

**Оценка времени**. приблизительно 3 минуты.

Дополнительные сведения о создании кластера AKS за пределами пакета SDK для Azure Machine Learning см. в разделе со следующими статьями:

* [Создание кластера AKS](https://docs.microsoft.com/cli/azure/aks?toc=%2Fen-us%2Fazure%2Faks%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Создание кластера AKS (портал)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

#### <a name="deploy-the-image"></a>Развертывание образа

Для развертывания образа из раздела [Создание образа](#createimage) данного документа в кластер серверов Azure Kubernetes используйте следующий код:

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
service = Webservice.deploy_from_image(workspace = ws,
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
service.wait_for_deployment(show_output = True)
print(service.state)
```

**Оценка времени**. приблизительно 3 минуты.

Дополнительные сведения см. в справочной документации по классам [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) и [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py).

### <a id="azuremlcompute"></a> Вывод с вычислений для машинного Обучения Azure

Целевые объекты вычисления в Azure ML создаются и управляются службой машинного обучения Azure. Они могут использоваться для прогнозирования пакета из конвейеров машинного Обучения Azure.

Пошаговое руководство, вывода пакетной службы с помощью вычислений машинного Обучения Azure, в статье [как выполнить пакетные прогнозы](how-to-run-batch-predictions.md) документа.


### <a id="fpga"></a> Развертывание в программируемых пользователем вентильных матрицах (ППВМ)

Project Brainwave позволяет добиться сверхнизкой задержки для запросов формирования выводов в режиме реального времени. Project Brainwave ускоряет обработку глубоких нейронных сетей (DNN), развернутых в программируемые пользователем вентильные матрицы в облаке Azure. Часто используемые DNN доступны в качестве характеризаторов для переноса обучения. Кроме того, их можно настроить с использованием весовых коэффициентов, обученных на основе ваших данных.

Пошаговое руководство по развертыванию модели с помощью Project Brainwave см. в статье [Как развернуть модель как веб-службу в FPGA с помощью Машинного обучения Azure](how-to-deploy-fpga-web-service.md).

## <a name="define-schema"></a>Определение схемы

Настраиваемых декораторов. может использоваться для [OpenAPI](https://swagger.io/docs/specification/about/) создания спецификации и входных данных типа обработки, при развертывании веб-службы. В `score.py` файл, обеспечения выборки входных данных и/или выходных данных в конструкторе для одного из объектов определенного типа, а также пример и тип используются для автоматического создания схемы. В настоящее время поддерживаются следующие типы:

* `pandas`
* `numpy`
* `pyspark`
* стандартный Python

Сначала убедитесь, необходимые зависимости для `inference-schema` пакет включены в ваш `env.yml` файла среды conda. В этом примере используется `numpy` параметр типа в схеме, таким образом общедоступный IP-адрес дополнительного `[numpy-support]` также устанавливается.

```python
%%writefile myenv.yml
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn
    - inference-schema[numpy-support]
```

Затем измените `score.py` файл для импорта `inference-schema` пакетов. Определите входные и выходные форматы образец в `input_sample` и `output_sample` переменные, которые представляют параметры запроса и ответа для веб-службы. Используйте эти примеры во входных данных и вывода декораторы функция на `run()` функции.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)


input_sample = np.array([[1.8]])
output_sample = np.array([43638.88])

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

После изучения обычный образ регистрации и веб-службы процесс развертывания с измененным `score.py` файл, извлеките Swagger uri из службы. Запрашивает этот uri будет возвращать `swagger.json` файл.

```python
service.wait_for_deployment(show_output=True)
print(service.swagger_uri)
```



При создании образа каждую службу, которая должна использовать его, необходимо обновить вручную. Чтобы обновить веб-службу, используйте метод `update`. Следующий код демонстрирует, как обновлять веб-службу для использования нового образа.

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

Дополнительные сведения см. в справочной документации по классу [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

## <a name="test-web-service-deployments"></a>Тестирование развертывания веб-служб

Чтобы протестировать развертывание веб-службы, можно использовать метод `run` объекта Webservice. В следующем примере документ JSON обозначается как веб-служба и отображается результат. Отправляемые данные должны соответствовать ожиданиям модели. В этом примере формат данных соответствует входным данным, ожидаемых от модели diabetes.

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data = test_sample)
print(prediction)
```

Веб-служба — это REST API, так что вы можете создавать клиентские приложения на различных языках программирования. Дополнительные сведения см. в статье [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md).

## <a id="update"></a> Обновление веб-службы

При создании образа каждую службу, которая должна использовать его, необходимо обновить вручную. Чтобы обновить веб-службу, используйте метод `update`. Следующий код демонстрирует, как обновлять веб-службу для использования нового образа.

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

Дополнительные сведения см. в справочной документации по классу [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

## <a id="iotedge"></a> Развертывание в Azure IoT Edge

Устройство Azure IoT Edge — это устройство под управлением Linux или Windows, которое запускает среду выполнения Azure IoT Edge. С помощью центра Интернета вещей Azure, можно развернуть модели машинного обучения для этих устройств как модули Edge Интернета вещей. Развертывание модели на устройстве IoT Edge позволяет устройству использовать эту модель напрямую, а не отправлять данные в облако для обработки. При этом сокращается время отклика и объем передачи данных.

Модули IoT Edge Azure развертываются на устройстве из реестра контейнеров. При создании образа из модели он хранится в реестре контейнеров рабочей области.

> [!IMPORTANT]
> Сведения в этом разделе предполагается, что вы уже знакомы с модулями центра Интернета вещей Azure и Azure IoT Edge. Хотя некоторые сведения в этом разделе относятся к службе машинного обучения Azure, большая часть процесса для развертывания на устройстве edge происходит в службы Интернета вещей Azure.
>
> Если вы не знакомы с помощью Интернета вещей Azure, см. в разделе [основы Интернета вещей Azure](https://docs.microsoft.com/azure/iot-fundamentals/) и [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) основные сведения. Затем используйте другие ссылки в этом разделе Дополнительные сведения о конкретных операций.

### <a name="set-up-your-environment"></a>Настройка среды

* Среда разработки. Дополнительные сведения см. в документе [Настройка среды разработки](how-to-configure-environment.md).

* [Центр Интернета вещей](../../iot-hub/iot-hub-create-through-portal.md) в подписке Azure.

* Обученная модель. Пример обучения модели см. в документе [Обучение модели классификации изображений с помощью машинного обучения Azure](tutorial-train-models-with-aml.md). Предварительно обученную модель можно найти в [репозитории GitHub набора средств для работы с искусственным интеллектом для Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial).

### <a id="getcontainer"></a> Получение учетных данных реестра контейнера

Для развертывания модуля IoT Edge на устройстве Azure IoT требуются учетные данные для реестра контейнеров, в котором Служба машинного обучения Azure хранит образы Docker.

Учетные данные можно получить двумя способами:

+ **На портале Azure.**

  1. Войдите на [портале Azure](https://portal.azure.com/signin/index).

  1. Перейдите в свою рабочую область Машинного обучения Azure и выберите __Обзор__. Чтобы перейти к параметрам реестра контейнеров, нажмите ссылку __Реестр__.

     ![Изображение записи реестра контейнеров](./media/how-to-deploy-and-where/findregisteredcontainer.png)

  1. В реестре контейнеров выберите раздел **Ключи доступа**, а затем включите учетную запись администратора.

     ![Изображение экрана ключей доступа](./media/how-to-deploy-and-where/findaccesskey.png)

  1. Сохраните значения для полей **вход на сервер**, **имя пользователя** и **пароль**.

+ **С помощью скрипта Python.**

  1. Используйте следующий сценарий Python после кода, выполненного ранее для создания контейнера:

     ```python
     # Getting your container details
     container_reg = ws.get_details()["containerRegistry"]
     reg_name=container_reg.split("/")[-1]
     container_url = "\"" + image.image_location + "\","
     subscription_id = ws.subscription_id
     from azure.mgmt.containerregistry import ContainerRegistryManagementClient
     from azure.mgmt import containerregistry
     client = ContainerRegistryManagementClient(ws._auth,subscription_id)
     result= client.registries.list_credentials(resource_group_name, reg_name, custom_headers=None, raw=False)
     username = result.username
     password = result.passwords[0].value
     print('ContainerURL{}'.format(image.image_location))
     print('Servername: {}'.format(reg_name))
     print('Username: {}'.format(username))
     print('Password: {}'.format(password))
     ```
  1. Сохраните значения ContainerURL, Servername, Username и Password.

     Эти учетные данные необходимы для предоставления устройству IoT Edge доступа к образам в частном реестре контейнеров.

### <a name="prepare-the-iot-device"></a>Подготовка устройства IoT

Регистрация устройства с центром Интернета вещей Azure, а затем установить среду выполнения IoT Edge на устройстве. Если вы не знакомы с этим процессом, см. в разделе [краткое руководство: Развертывание первого модуля IoT Edge на устройстве Linux x64](../../iot-edge/quickstart-linux.md).

Ниже приведены другие методы для регистрации устройства.

* [портал Azure](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)
* [Интерфейс командной строки Azure](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-cli)
* [Visual Studio Code](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-vscode)

### <a name="deploy-the-model-to-the-device"></a>Развертывание модели на устройстве

Развертывание модели на устройстве, используйте данные реестра, собранных в [получить учетные данные реестра контейнера](#getcontainer) раздела с помощью развертывания модуля действия для модулей Edge Интернета вещей. Например, если [развертывание Azure модули Edge Интернета вещей на портале Azure](../../iot-edge/how-to-deploy-modules-portal.md), необходимо настроить __параметры реестра__ для устройства. Используйте __сервер входа__, __username__, и __пароль__ для реестра контейнеров рабочей области.

Кроме того, можно развернуть с помощью [Azure CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-cli) и [Visual Studio Code](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-vscode).

## <a name="clean-up"></a>Очистка

Для удаления развернутой веб-службы используйте `service.delete()`.

Для удаления образа используйте `image.delete()`.

Чтобы удалить зарегистрированную модель, используйте `model.delete()`.

Дополнительные сведения см. в справочной документации по классам [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), [Image.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#delete--) и [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Дальнейшие действия

* [Устранение неполадок развертывания](how-to-troubleshoot-deployment.md)
* [Защита веб-служб Машинного обучения Azure с помощью SSL](how-to-secure-web-service.md)
* [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md)
* [Run batch predictions on large data sets with Azure Machine Learning service](how-to-run-batch-predictions.md) (Составление пакетных прогнозов для больших наборов данных с помощью Службы машинного обучения Azure)
* [Мониторинг моделей машинного обучения в Azure с помощью Application Insights](how-to-enable-app-insights.md)
* [Сбор данных для моделей в рабочей среде](how-to-enable-data-collection.md)
* [What is the Azure Machine Learning SDK for Python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) (Что такое пакет SDK Службы машинного обучения Azure для Python?)
* [Securely run experiments and inferencing inside an Azure Virtual Network](how-to-enable-virtual-network.md) (Безопасное выполнение экспериментов и формирование выводов внутри виртуальной сети Azure)
* [Recommenders](https://github.com/Microsoft/Recommenders) (Системы рекомендаций)
* [Создание API рекомендаций в режиме реального времени в Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
