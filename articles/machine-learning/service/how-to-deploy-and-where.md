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
ms.openlocfilehash: 17193bf3285a2052a913293ec3adc6f9b8884f72
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53435954"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Развертывание моделей с помощью Службы машинного обучения Azure

Служба машинного обучения Azure предоставляет несколько способов развертывания обученной модели с помощью пакета SDK. В этом документе вы научитесь развертывать модель как веб-службу в облаке Azure и на устройствах IoT Edge.

Вы можете развертывать модели в следующих целевых средах вычислений:

| Целевой объект вычисления | Тип развертывания | ОПИСАНИЕ |
| ----- | ----- | ----- |
| [Экземпляры контейнеров Azure (ACI)](#aci) | Веб-служба | Быстрое развертывание. Идеальны для разработки и (или) тестирования. |
| [Служба Azure Kubernetes (AKS)](#aks) | Веб-служба | Подходит для крупномасштабных рабочих развертываний. Она обеспечивает автоматическое масштабирование и малое время отклика. |
| [Edge Интернета вещей Azure](#iotedge) | Модуль Интернета вещей | Развертывает модели на устройствах Интернета вещей. Формирование выводов происходит на устройстве. |
| [Программируемая пользователем вентильная матрица(ППВМ).](#fpga) | Веб-служба | Сверхнизкая задержка для формирования выводов в режиме реального времени. |

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Kwk3]

## <a name="prerequisites"></a>Предварительные требования

- Должны быть установлены рабочая область службы "Машинное обучение Azure" и пакет SDK Машинного обучения Azure для Python. Узнать, как получить эти компоненты, можно в [кратком руководстве по началу работы с машинным обучением Azure](quickstart-get-started.md).

- Обученная модель в формате сериализованного файла (`.pkl`) или ONNX (`.onnx`). Если у вас нет обученной модели, обучите и зарегистрируйте ее в Службе машинного обучения Azure в соответствии с инструкциями в учебнике [Обучение моделей](tutorial-train-models-with-aml.md).

- В разделах кода предполагается, что `ws` ссылается на рабочую область машинного обучения. Например, `ws = Workspace.from_config()`.

## <a name="deployment-workflow"></a>Рабочий процесс развертывания

Процесс развертывания модели аналогичен для всех целевых объектов вычислений:

1. Обучение модели.
1. Регистрация модели.
1. Создание конфигурации образа.
1. Создайте образ.
1. Развертывание образа в целевом объекте вычислений.
1. тестирование развертывания
1. Удаление артефактов (необязательно).

    * Для **развертывания в качестве веб-службы** существует три варианта:

        * [deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-). При использовании этого метода нет необходимости регистрировать модель или создавать образ. Тем не менее при этом нет возможности настроить имя модели, образ или соответствующие теги и описания.
        * [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-). При использовании этого метода нет необходимости создавать образ. Однако при этом нет возможности настроить имя создаваемой модели.
        * [deploy_from_image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-). Перед использованием этого метода следует зарегистрировать модель и создать образ.

        В примерах, приведенных в этом документе, используется `deploy_from_image`.

    * При **развертывании в качестве модуля IoT Edge** необходимо зарегистрировать модель и создать образ.

## <a name="register-a-model"></a>Регистрация модели

Развертывать можно только обученные модели. Модель можно обучить с помощью Машинного обучения Azure или другой службы. Для регистрации модели из файла используйте следующий код:

```python
from azureml.core.model import Model

model = Model.register(model_path = "model.pkl",
                       model_name = "Mymodel",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)
```

> [!NOTE]
> Хотя в примере показано использование модели, сохраненной в качестве сериализованного файла, вы также можете использовать модели ONNX. Дополнительные сведения об их использовании см. в статье [ONNX и машинное обучение Azure: создание и развертывание совместимых моделей AI](how-to-build-deploy-onnx.md).

Дополнительные сведения см. в справочной документации по [классу Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a id="configureimage"></a> Создание конфигурации образа

Развернутые модели упаковываются в виде образа. Образ содержит зависимости, необходимые для запуска модели.

Для создания конфигурации образа развертываний **экземпляра контейнера Azure**, **Службы Azure Kubernetes** и **Azure IoT Edge** используется класс `azureml.core.image.ContainerImage`. Затем конфигурация образа используется для создания образа Docker. 

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

В этой конфигурации для передачи запросов в модель используется файл `score.py`. Этот файл содержит две функции:

* `init()`: Обычно эта функция загружает модель в глобальный объект. Эта функция выполняется только один раз при запуске контейнера Docker. 

* `run(input_data)`: Эта функция использует модель для прогнозирования значения на основе входных данных. Входные и выходные данные для запуска обычно используют JSON для сериализации и десериализации, но поддерживаются и другие форматы.

Пример файла `score.py` см. в [учебнике по классификации образов](tutorial-deploy-models-with-aml.md#make-script). Пример с использованием модели ONNX см. в статье [ONNX и машинное обучение Azure: создание и развертывание совместимых моделей AI](how-to-build-deploy-onnx.md).

Параметр `conda_file` используется для предоставления файла среды Conda. Этот файл определяет среду Conda для развернутой модели. Дополнительные сведения о создании этого файла см. в разделе [Создание файла среды (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file).

Дополнительные сведения см. в справочной документации по [классу ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py).

## <a id="createimage"></a> Создание образа

После создания конфигурации образа его можно использовать для создания образа. Этот образ хранится в реестре контейнеров рабочей области. После создания образа его можно развертывать для нескольких служб.

```python
# Create the image from the image configuration
image = ContainerImage.create(name = "myimage", 
                              models = [model], #this is the model object
                              image_config = image_config,
                              workspace = ws
                              )
```

**Примерное время**: приблизительно 3 минуты.

При регистрации нескольких образов с тем же именем версии образов присваиваются автоматически. Например, первому образу, зарегистрированному как `myimage`, присваивается идентификатор `myimage:1`. При следующей регистрации образа с именем `myimage` идентификатором нового образа будет `myimage:2`.

Дополнительные сведения см. в справочной документации по [классу ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py).

## <a name="deploy-the-image"></a>Развертывание образа

На этапе развертывания процесс немного отличается в зависимости от целевого объекта вычислений, на котором оно выполняется. Из следующих разделов вы узнаете, как развернуть:

* [Экземпляры контейнеров Azure](#aci);
* [Службы Azure Kubernetes](#aks);
* [Project Brainwave (программируемые пользователем вентильные матрицы)](#fpga);
* [устройства Azure IoT Edge](#iotedge).

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

Дополнительные сведения см. в справочной документации по классам [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) и [Webservice](https://docs.microsoft.comS/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py).

### <a id="aks"></a> Развертывание в Службе Azure Kubernetes

Для развертывания модели в качестве крупномасштабной рабочей веб-службы используется Служба Azure Kubernetes (AKS). Можно использовать существующий кластер AKS или создать его с помощью пакета SDK для службы "Машинное обучение Azure", CLI или портала Azure.

Кластер AKS для рабочей области нужно создать только один раз. Один кластер можно использовать для нескольких развертываний. Если вы удаляете кластер, во время следующего развертывания нужно будет создать новый кластер.

Служба Azure Kubernetes предоставляет следующие возможности.

* Автомасштабирование
* Ведение журналов
* Сбор данных модели
* Малое время отклика для веб-служб

Для развертывания в Службе Azure Kubernetes следуйте приведенным ниже инструкциям:

1. Чтобы создать кластер AKS, выполните следующий код:

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

    > [!TIP]
    > Если в вашей подписке Azure уже есть кластер AKS версии 1.11.*, его можно использовать для развертывания вашего образа. В следующем коде показано, как присоединить имеющийся кластер к рабочей области.
    >
    > ```python
    > # Set the resource group that contains the AKS cluster and the cluster name
    > resource_group = 'myresourcegroup'
    > cluster_name = 'mycluster'
    > 
    > # Attatch the cluster to your workgroup
    > attach_config = AksCompute.attach_configuration(resource_group = resource_group,
    >                                          cluster_name = cluster_name)
    > compute = ComputeTarget.attach(ws, 'mycompute', attach_config)
    > 
    > # Wait for the operation to complete
    > aks_target.wait_for_completion(True)
    > ```

2. Для развертывания образа из раздела [Создание образа](#createimage) данного документа используйте следующий код:

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

    > [!TIP]
    > Если во время развертывания возникают ошибки, используйте `service.get_logs()` для просмотра журналов службы AKS. Информация в этих журналах может указывать на причину ошибки.

Дополнительные сведения см. в справочной документации по классам [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) и [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py).

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
sudo ./createNregister <The Azure subscriptionID you wnat to use> <Resourcegroup to use or create for the IoT hub> <Azure location to use e.g. eastus2> <the Hub ID you want to use or create> <the device ID you want to create>
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

## <a name="update-the-web-service"></a>Обновление веб-службы

Чтобы обновить веб-службу, используйте метод `update`. Следующий код демонстрирует, как обновлять веб-службу для использования нового образа.

```python
from azureml.core.webservice import Webservice

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)
# Update the image used by the service
service.update(image = new-image)
print(service.state)
```

> [!NOTE]
> При обновлении образа веб-служба автоматически не обновляется. Каждую службу, которая должна использовать новый образ, необходимо обновить вручную.

## <a name="clean-up"></a>Очистка

Для удаления развернутой веб-службы используйте `service.delete()`.

Для удаления образа используйте `image.delete()`.

Чтобы удалить зарегистрированную модель, используйте `model.delete()`.

## <a name="next-steps"></a>Дополнительная информация

* [Защита веб-служб Машинного обучения Azure с помощью SSL](how-to-secure-web-service.md)
* [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md)
* [Run batch predictions on large data sets with Azure Machine Learning service](how-to-run-batch-predictions.md) (Составление пакетных прогнозов для больших наборов данных с помощью Службы машинного обучения Azure)
