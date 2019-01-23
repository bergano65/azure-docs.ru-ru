---
title: Развертывание моделей в качестве веб-служб
titleSuffix: Azure Machine Learning service
description: 'Сведения о том, как и где следует развертывать модели Службы машинного обучения Azure, в том числе: Экземпляры контейнеров Azure, Службу Azure Kubernetes, Azure IoT Edge и программируемые пользователем вентильные матрицы.'
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 7fc40945588c272ae0ae80ba17b7b3752cab4306
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353317"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Развертывание моделей с помощью Службы машинного обучения Azure

Служба машинного обучения Azure предоставляет несколько способов развертывания обученной модели с помощью пакета SDK. В этом документе вы научитесь развертывать модель как веб-службу в облаке Azure и на устройствах IoT Edge.

> [!IMPORTANT]
> Предоставление общего доступа к ресурсам независимо от источника (CORS) при развертывании модели в качестве веб-службы сейчас не поддерживается.

Вы можете развертывать модели в следующих целевых средах вычислений:

| Целевой объект вычисления | Тип развертывания | ОПИСАНИЕ |
| ----- | ----- | ----- |
| [Экземпляры контейнеров Azure (ACI)](#aci) | Веб-служба | Быстрое развертывание. Идеальны для разработки и (или) тестирования. |
| [Служба Azure Kubernetes (AKS)](#aks) | Веб-служба | Подходит для крупномасштабных рабочих развертываний. Она обеспечивает автоматическое масштабирование и малое время отклика. |
| [Edge Интернета вещей Azure](#iotedge) | Модуль Интернета вещей | Развертывает модели на устройствах Интернета вещей. Формирование выводов происходит на устройстве. |
| [Программируемая пользователем вентильная матрица(ППВМ).](#fpga) | Веб-служба | Сверхнизкая задержка для формирования выводов в режиме реального времени. |

Процесс развертывания модели аналогичен для всех целевых объектов вычислений:

1. Обучение и регистрация модели.
1. Настройка и регистрация образа, использующего модель.
1. Развертывание образа в целевом объекте вычислений.
1. тестирование развертывания

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Kwk3]


Дополнительные сведения об основных понятиях, связанных с рабочим процессом развертывания, см. в статье [Администрирование, развертывание и мониторинг моделей с помощью службы "Машинное обучение Azure"](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](http://aka.ms/AMLFree).

- Должны быть установлены рабочая область службы "Машинное обучение Azure" и пакет SDK Машинного обучения Azure для Python. Узнать, как получить эти компоненты, можно в [кратком руководстве по началу работы с машинным обучением Azure](quickstart-get-started.md).

- Обученная модель. Если у вас нет обученной модели, обучите и зарегистрируйте ее в Службе машинного обучения Azure в соответствии с инструкциями в учебнике [Обучение моделей](tutorial-train-models-with-aml.md).

    > [!NOTE]
    > Хотя Служба машинного обучения Azure может работать с любой общей моделью, которую можно загрузить в Python 3, в примерах в этом документе используется модель, сохраненная в сериализованном формате.
    > 
    > Дополнительные сведения об их использовании см. в статье [ONNX и машинное обучение Azure: создание и развертывание совместимых моделей AI](how-to-build-deploy-onnx.md).

## <a id="registermodel"></a> Регистрация обученной модели

Реестр моделей позволяет хранить и упорядочивать обученные модели в облаке Azure. Модели зарегистрированы в рабочей области Службы машинного обучения Azure. Модель можно обучить с помощью Машинного обучения Azure или другой службы. Для регистрации модели из файла используйте следующий код:

```python
from azureml.core.model import Model

model = Model.register(model_path = "model.pkl",
                       model_name = "Mymodel",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)
```

**Оценка времени**. Примерно 10 секунд.

Дополнительные сведения см. в справочной документации по [классу Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a id="configureimage"></a> Создание и регистрация образа

Развернутые модели упаковываются в виде образа. Образ содержит зависимости, необходимые для запуска модели.

Для создания конфигурации образа развертываний **экземпляра контейнера Azure**, **Службы Azure Kubernetes** и **Azure IoT Edge** используется класс [azureml.core.image.ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py). Затем конфигурация образа используется для создания образа Docker. 

В приведенном ниже коде показано, как создать новую конфигурацию образа.

```python
from azureml.core.image import ContainerImage

# Image configuration
image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                 runtime = "python",
                                                 conda_file = "myenv.yml",
                                                 description = "Image with ridge regression model",
                                                 tags = {"data": "diabetes", "type": "regression"}
                                                 )
```

**Оценка времени**. Примерно 10 секунд.

В следующей таблице описаны важные параметры, используемые в этом примере:

| Параметр | ОПИСАНИЕ |
| ----- | ----- |
| `execution_script` | Указывает сценарий Python, который используется для получения запросов, отправленных в службу. В этом примере сценарий содержится в файле `score.py`. Дополнительные сведения см. в разделе [Сценарий выполнения](#script). |
| `runtime` | Указывает, что образ использует Python. Другой вариант — `spark-py`, который использует Python с Apache Spark. |
| `conda_file` | Используется для предоставления файла среды Conda. Этот файл определяет среду Conda для развернутой модели. Дополнительные сведения о создании этого файла см. в разделе [Создание файла среды (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file). |

Дополнительные сведения см. в справочной документации по [классу ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py).

### <a id="script"></a> Сценарий выполнения

Сценарий выполнения получает данные, отправленные в развернутый образ, и передает их в модель. Затем он принимает ответ, возвращенный моделью, и возвращает его клиенту. Сценарий относится только к модели. Ему необходимо распознать данные, которые модель ожидает и возвращает. Сценарий обычно содержит две функции, которые загружают и запускают модель:

* `init()`: Обычно эта функция загружает модель в глобальный объект. Эта функция выполняется только один раз при запуске контейнера Docker. 

* `run(input_data)`: Эта функция использует модель для прогнозирования значения на основе входных данных. Ко входным и выходным данным для запуска обычно применяется формат JSON для сериализации и десериализации. Вы также можете работать с необработанными двоичными данными. Вы можете преобразовать данные, прежде чем отправлять их в модель или возвращать клиенту. 

#### <a name="working-with-json-data"></a>Работа с данными JSON

Ниже приведен пример сценария, который принимает и возвращает данные JSON. Функция `run` преобразует данные из JSON в формат, ожидаемый моделью, а затем преобразует ответ JSON перед его возвратом:

```python
# import things required by this script
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

Если ваша модель принимает __двоичные данные__, используйте `AMLRequest`, `AMLResponse` и `rawhttp`. Ниже приведен пример сценария, который принимает двоичные данные и возвращает обращенные байты для запросов POST. Для запросов GET он возвращает полный URL-адрес в тексте ответа:

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

## <a id="deploy"></a> Развертывание образа

На этапе развертывания процесс немного отличается в зависимости от целевого объекта вычислений, на котором оно выполняется. Из следующих разделов вы узнаете, как развернуть:

* [Экземпляры контейнеров Azure](#aci);
* [Службы Azure Kubernetes](#aks);
* [Project Brainwave (программируемые пользователем вентильные матрицы)](#fpga);
* [устройства Azure IoT Edge](#iotedge).

> [!NOTE]
> Для **развертывания в качестве веб-службы** существует три метода, которые вы можете использовать:
>
> | Метод | Примечания |
> | ----- | ----- |
> | [deploy_from_image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-) | Перед использованием этого метода следует зарегистрировать модель и создать образ. |
> | [deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) | При использовании этого метода нет необходимости регистрировать модель или создавать образ. Тем не менее при этом нет возможности настроить имя модели, образ или соответствующие теги и описания. |
> | [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) | При использовании этого метода нет необходимости создавать образ. Однако при этом нет возможности настроить имя создаваемой модели. |
>
> В примерах, приведенных в этом документе, используется `deploy_from_image`.

### <a id="aci"></a> Развертывание в службе "Экземпляры контейнеров Azure"

Экземпляры контейнеров Azure (ACI) можно использовать для развертывания моделей в качестве веб-службы, если выполняется одно или несколько из следующих условий:

- вам важно быстро выполнять развертывание и проверку модели. Развертывание в ACI занимает менее 5 минут;
- вы тестируете модель, которая находится в стадии разработки. Дополнительные сведения о квотах и доступности по регионам для службы "Экземпляры контейнеров Azure" см. в [этой статье](https://docs.microsoft.com/azure/container-instances/container-instances-quotas).

Для развертывания в службе "Экземпляры контейнеров Azure" следуйте приведенным ниже инструкциям.

1. Определите конфигурацию развертывания. В следующем примере определяется конфигурация c одним ядром ЦП и 1 ГБ памяти:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configAci)]

2. Для развертывания образа из раздела [Создание образа](#createimage) данного документа используйте следующий код:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3Deploy)]

    **Примерное время**: приблизительно 3 минуты.

    > [!TIP]
    > Если во время развертывания возникают ошибки, используйте `service.get_logs()` для просмотра журналов службы AKS. Информация в этих журналах может указывать на причину ошибки.

Дополнительные сведения см. в справочной документации по классам [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) и [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

### <a id="aks"></a> Развертывание в Службе Azure Kubernetes

Для развертывания модели в качестве крупномасштабной рабочей веб-службы используется Служба Azure Kubernetes (AKS). Можно использовать существующий кластер AKS или создать его с помощью пакета SDK для службы "Машинное обучение Azure", CLI или портала Azure.

Кластер AKS для рабочей области нужно создать только один раз. Один кластер можно использовать для нескольких развертываний. Если вы удаляете кластер, во время следующего развертывания нужно будет создать новый кластер.

Служба Azure Kubernetes предоставляет следующие возможности.

* Автомасштабирование
* Ведение журналов
* Сбор данных модели
* Малое время отклика для веб-служб

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

Если в вашей подписке Azure уже есть кластер AKS версии 1.11.*, его можно использовать для развертывания вашего образа. В следующем коде показано, как присоединить имеющийся кластер к рабочей области.

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attatch the cluster to your workgroup
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)

# Wait for the operation to complete
aks_target.wait_for_completion(True)
```

**Оценка времени**. приблизительно 3 минуты.

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

> [!TIP]
> Если во время развертывания возникают ошибки, используйте `service.get_logs()` для просмотра журналов службы AKS. Информация в этих журналах может указывать на причину ошибки.

Дополнительные сведения см. в справочной документации по классам [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) и [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py).

### <a id="fpga"></a> Развертывание в программируемых пользователем вентильных матрицах (ППВМ)

Project Brainwave позволяет добиться сверхнизкой задержки для запросов формирования выводов в режиме реального времени. Project Brainwave ускоряет обработку глубоких нейронных сетей (DNN), развернутых в программируемые пользователем вентильные матрицы в облаке Azure. Часто используемые DNN доступны в качестве характеризаторов для переноса обучения. Кроме того, их можно настроить с использованием весовых коэффициентов, обученных на основе ваших данных.

Пошаговое руководство по развертыванию модели с помощью Project Brainwave см. в статье [Как развернуть модель как веб-службу в FPGA с помощью Машинного обучения Azure](how-to-deploy-fpga-web-service.md).

### <a id="iotedge"></a> Развертывание в Azure IoT Edge

Устройство Azure IoT Edge — это устройство под управлением Linux или Windows, которое запускает среду выполнения Azure IoT Edge. Модели машинного обучения могут развертываться на этих устройствах как модули IoT Edge. Развертывание модели на устройстве IoT Edge позволяет устройству использовать эту модель напрямую, а не отправлять данные в облако для обработки. При этом сокращается время отклика и объем передачи данных.

Модули IoT Edge Azure развертываются на устройстве из реестра контейнеров. При создании образа из модели он хранится в реестре контейнеров рабочей области.

#### <a name="set-up-your-environment"></a>Настройка среды

* Среда разработки. Дополнительные сведения см. в документе [Настройка среды разработки](how-to-configure-environment.md).

* [Центр Интернета вещей](../../iot-hub/iot-hub-create-through-portal.md) в подписке Azure. 

* Обученная модель. Пример обучения модели см. в документе [Обучение модели классификации изображений с помощью машинного обучения Azure](tutorial-train-models-with-aml.md). Предварительно обученную модель можно найти в [репозитории GitHub набора средств для работы с искусственным интеллектом для Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial).

#### <a name="prepare-the-iot-device"></a>Подготовка устройства IoT
Необходимо создать Центр Интернета вещей и зарегистрировать устройство или повторно использовать имеющееся с помощью [этого сценария](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/createNregister).

``` bash
ssh <yourusername>@<yourdeviceip>
sudo wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/createNregister
sudo chmod +x createNregister
sudo ./createNregister <The Azure subscriptionID you want to use> <Resourcegroup to use or create for the IoT hub> <Azure location to use e.g. eastus2> <the Hub ID you want to use or create> <the device ID you want to create>
```

Сохраните результирующую строку подключения после "cs":"{copy this string}".

Инициализируйте устройство, загрузив [этот сценарий](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/installIoTEdge) в граничный узел Интернета вещей UbuntuX64 или Виртуальную машину для обработки и анализа данных для выполнения следующих команд:

```bash
ssh <yourusername>@<yourdeviceip>
sudo wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/installIoTEdge
sudo chmod +x installIoTEdge
sudo ./installIoTEdge
```

Узел IoT Edge готов принимать строку подключения для Центра Интернета вещей. Найдите строку ```device_connection_string:``` и вставьте указанную выше строку подключения в кавычках.

Изучив документ [Краткое руководство. Развертывание модуля IoT Edge на устройстве под управлением 64-разрядной ОС Linux](../../iot-edge/quickstart-linux.md), вы также можете узнать, как зарегистрировать свое устройство и установить среду выполнения IoT шаг за шагом.


#### <a name="get-the-container-registry-credentials"></a>Получение учетных данных реестра контейнеров
Для развертывания модуля IoT Edge на устройстве Azure IoT требуются учетные данные для реестра контейнеров, в котором Служба машинного обучения Azure хранит образы Docker.

Необходимые учетные данные реестра контейнеров можно легко получить двумя способами:

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

#### <a name="deploy-the-model-to-the-device"></a>Развертывание модели на устройстве

Модель можно легко развернуть, выполнив [этот скрипт](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/deploymodel) и указав следующие сведения (их описание приведено выше): имя реестра контейнеров, имя пользователя, пароль, URL-адрес образа, требуемое имя развертывания, имя Центра Интернета вещей и идентификатор устройства, который вы создали. Для этого выполните следующие действия на виртуальной машине. 

```bash 
wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/deploymodel
sudo chmod +x deploymodel
sudo ./deploymodel <ContainerRegistryName> <username> <password> <imageLocationURL> <DeploymentID> <IoTHubname> <DeviceID>
```

Кроме того, для развертывания образа на устройстве можно выполнить действия, приведенные в документе [Развертывание модулей IoT Edge Azure с помощью портала Azure](../../iot-edge/how-to-deploy-modules-portal.md). Настраивая __параметры реестра__ для устройства, используйте значения __вход на сервер__, __имя пользователя__ и __пароль__ для реестра контейнеров рабочей области.

> [!NOTE]
> Для получения сведений о начале работы со службой Azure IoT см. следующие документы:
>
> * [Краткое руководство. Развертывание модуля IoT Edge на устройстве под управлением ОС Linux](../../iot-edge/quickstart-linux.md)
> * [Краткое руководство. Развертывание модуля IoT Edge на устройстве с Windows на портале Azure (предварительная версия)](../../iot-edge/quickstart.md)


## <a name="testing-web-service-deployments"></a>Тестирование развертываний веб-служб

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

Чтобы обновить веб-службу, используйте метод `update`. Следующий код демонстрирует, как обновлять веб-службу для использования нового образа.

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

> [!NOTE]
> При обновлении образа веб-служба автоматически не обновляется. Каждую службу, которая должна использовать новый образ, необходимо обновить вручную.

Дополнительные сведения см. в справочной документации по классу [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

## <a name="clean-up"></a>Очистка

Для удаления развернутой веб-службы используйте `service.delete()`.

Для удаления образа используйте `image.delete()`.

Чтобы удалить зарегистрированную модель, используйте `model.delete()`.

Дополнительные сведения см. в справочной документации по классам [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), [Image.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#delete--) и [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Дополнительная информация

* [Защита веб-служб Машинного обучения Azure с помощью SSL](how-to-secure-web-service.md)
* [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md)
* [Run batch predictions on large data sets with Azure Machine Learning service](how-to-run-batch-predictions.md) (Составление пакетных прогнозов для больших наборов данных с помощью Службы машинного обучения Azure)
* [Мониторинг моделей машинного обучения в Azure с помощью Application Insights](how-to-enable-app-insights.md)
* [Сбор данных для моделей в рабочей среде](how-to-enable-data-collection.md)
* [What is the Azure Machine Learning SDK for Python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) (Что такое пакет SDK Службы машинного обучения Azure для Python?)
* [Securely run experiments and inferencing inside an Azure Virtual Network](how-to-enable-virtual-network.md) (Безопасное выполнение экспериментов и формирование выводов внутри виртуальной сети Azure)
