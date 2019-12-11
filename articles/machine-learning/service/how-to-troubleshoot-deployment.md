---
title: Руководство по устранению неполадок с развертыванием
titleSuffix: Azure Machine Learning
description: Узнайте, как обойти и устранить распространенные ошибки развертывания DOCKER с помощью службы Kubernetes Azure и экземпляров контейнеров Azure, используя Машинное обучение Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 10/25/2019
ms.custom: seodec18
ms.openlocfilehash: 7d7744aab6d83b3415f32ca9e311940836c6f93c
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997247"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Устранение неполадок Машинное обучение Azure службы Azure Kubernetes и развертывания экземпляров контейнеров Azure

Узнайте, как обойти или устранить распространенные ошибки развертывания DOCKER с помощью службы "экземпляры контейнеров Azure" (ACI) и Azure Kubernetes Service (AKS), используя Машинное обучение Azure.

При развертывании модели в Машинное обучение Azure система выполняет ряд задач. Ниже перечислены задачи развертывания.

1. Регистрация модели в реестре моделей рабочей области.

2. Создание образа Docker, включая:
    1. загрузку зарегистрированной модели из реестра; 
    2. создание файла dockerfile в среде Python на основе зависимостей, указываемых в yaml-файле среды;
    3. добавление файлов модели и скрипта оценки, которые содержатся в файле dockerfile;
    4. создание нового образа Docker с помощью файла dockerfile;
    5. регистрацию образа Docker с помощью реестра контейнеров Azure, связанного с рабочей областью.

    > [!IMPORTANT]
    > В зависимости от кода создание изображений происходит автоматически без ввода.

3. Развертывание образа Docker в службу "Экземпляр контейнера Azure" (ACI) или службу Azure Kubernetes (AKS).

4. Запуск новых контейнеров в ACI или AKS. 

Дополнительные сведения об этом процессе см. во введении в [Управление моделями](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Технические условия

* **Подписка Azure**. Если у вас ее нет, попробуйте [бесплатную или платную версию машинное обучение Azure](https://aka.ms/AMLFree).
* [Пакет SDK для машинное обучение Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Расширение CLI для машинное обучение Azure](reference-azure-machine-learning-cli.md).
* Для локальной отладки необходимо иметь рабочую установку DOCKER в локальной системе.

    Чтобы проверить установку DOCKER, используйте команду `docker run hello-world` из терминала или из командной строки. Сведения об установке DOCKER или устранении ошибок DOCKER см. в [документации по DOCKER](https://docs.docker.com/).

## <a name="before-you-begin"></a>Перед началом работы

Если возникнут какие-либо проблемы, прежде всего следует разбить задачу развертывания (описанную ранее) на отдельные шаги, чтобы установить причину проблемы.

Разбиение развертывания на задачи полезно, если вы используете API [WebService. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none--overwrite-false-) или [WebService. deploy_from_model ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none--overwrite-false-) , так как обе эти функции выполняют вышеупомянутые действия как одно действие. Обычно эти интерфейсы API удобны, но это позволяет разбиваться на действия при устранении неполадок, заменяя их приведенными ниже вызовами API.

1. Регистрация модели. Ниже приведен пример кода.

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Создание образа. Ниже приведен пример кода.

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      entry_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=image_config, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. Развертывание образа в качестве службы. Ниже приведен пример кода.

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

После разбиения процесса развертывания на отдельные задачи можно взглянуть на самые распространенные ошибки.

## <a name="image-building-fails"></a>Сбой создания образа

Если образ DOCKER не может быть построен, вызов [Image. wait_for_creation ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) или [Service. wait_for_deployment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-) завершается с некоторыми сообщениями об ошибках, которые могут предложить некоторые подсказки. Более подробную информацию об ошибках также можно найти в журнале сборки образа. Ниже приведен пример кода, в котором отображено обнаружение кода URI журнала сборки образа.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print(img.name, img.version, img.image_build_log_uri)
```

Код URI журнала образа — это URL-адрес SAS, указывающий на файл журнала, сохраненный в хранилище BLOB-объектов Azure. Просто скопируйте код URI и вставьте его в окно браузера, и вы сможете загрузить и просмотреть файл журнала.

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Политики доступа Azure Key Vault и шаблоны Azure Resource Manager

Сборка образа может также завершиться сбоем из-за проблемы с политикой доступа на Azure Key Vault. Такая ситуация может возникнуть при использовании шаблона Azure Resource Manager для создания рабочей области и связанных ресурсов (включая Azure Key Vault) несколько раз. Например, использование шаблона несколько раз с теми же параметрами, что и часть конвейера непрерывной интеграции и развертывания.

Большинство операций создания ресурсов через шаблоны идемпотентными, но Key Vault удаляет политики доступа каждый раз при использовании шаблона. Очистка политик доступа нарушает доступ к Key Vault для любой существующей рабочей области, которая ее использует. Это состояние приводит к ошибкам при попытке создания новых образов. Ниже приведены примеры ошибок, которые можно получить.

__Портал__.
```text
Create image "myimage": An internal server error occurred. Please try again. If the problem persists, contact support.
```

__Пакет SDK__:
```python
image = ContainerImage.create(name = "myimage", models = [model], image_config = image_config, workspace = ws)
Creating image
Traceback (most recent call last):
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 341, in create
    resp.raise_for_status()
  File "C:\Python37\lib\site-packages\requests\models.py", line 940, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 500 Server Error: Internal Server Error for url: https://eastus.modelmanagement.azureml.net/api/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/images?api-version=2018-11-19

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 346, in create
    'Content: {}'.format(resp.status_code, resp.headers, resp.content))
azureml.exceptions._azureml_exception.WebserviceException: Received bad response from Model Management Service:
Response Code: 500
Headers: {'Date': 'Tue, 26 Feb 2019 17:47:53 GMT', 'Content-Type': 'application/json', 'Transfer-Encoding': 'chunked', 'Connection': 'keep-alive', 'api-supported-versions': '2018-03-01-preview, 2018-11-19', 'x-ms-client-request-id': '3cdcf791f1214b9cbac93076ebfb5167', 'x-ms-client-session-id': '', 'Strict-Transport-Security': 'max-age=15724800; includeSubDomains; preload'}
Content: b'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}'
```

__CLI__:
```text
ERROR: {'Azure-cli-ml Version': None, 'Error': WebserviceException('Received bad response from Model Management Service:\nResponse Code: 500\nHeaders: {\'Date\': \'Tue, 26 Feb 2019 17:34:05
GMT\', \'Content-Type\': \'application/json\', \'Transfer-Encoding\': \'chunked\', \'Connection\': \'keep-alive\', \'api-supported-versions\': \'2018-03-01-preview, 2018-11-19\', \'x-ms-client-request-id\':
\'bc89430916164412abe3d82acb1d1109\', \'x-ms-client-session-id\': \'\', \'Strict-Transport-Security\': \'max-age=15724800; includeSubDomains; preload\'}\nContent:
b\'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}\'',)}
```

Чтобы избежать этой проблемы, рекомендуется использовать один из следующих подходов.

* Не развертывайте шаблон более одного раза для одних и тех же параметров. Или удалите существующие ресурсы перед использованием шаблона для их повторного создания.
* Изучите политики доступа Key Vault, а затем используйте эти политики, чтобы задать свойство `accessPolicies` шаблона.
* Убедитесь, что Key Vault ресурс уже существует. Если это так, не создавайте его повторно с помощью шаблона. Например, добавьте параметр, который позволяет отключить создание Key Vault ресурса, если он уже существует.

## <a name="debug-locally"></a>Отладка в локальной среде

Если возникли проблемы при развертывании модели в ACI или AKS, попробуйте развернуть ее как локальную веб-службу. Использование локальной веб-службы упрощает устранение неполадок. Образ DOCKER, содержащий модель, загружается и запускается в локальной системе.

> [!WARNING]
> Локальные развертывания веб-служб не поддерживаются в рабочих сценариях.

Чтобы выполнить развертывание локально, измените код, чтобы использовать `LocalWebservice.deploy_configuration()` для создания конфигурации развертывания. Затем используйте `Model.deploy()` для развертывания службы. В следующем примере выполняется развертывание модели (содержащейся в переменной `model`) в качестве локальной веб-службы:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice

# Create inference configuration. This creates a docker image that contains the model.
inference_config = InferenceConfig(runtime="python",
                                   entry_script="score.py",
                                   conda_file="myenv.yml")

# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

На этом этапе вы можете работать со службой в нормальном режиме. Например, следующий код демонстрирует отправку данных в службу:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

### <a name="update-the-service"></a>Обновление службы

Во время локального тестирования может потребоваться обновить файл `score.py`, чтобы добавить ведение журнала или попробовать устранить обнаруженные проблемы. Чтобы перезагрузить изменения в файл `score.py`, используйте `reload()`. Например, следующий код перезагружает скрипт для службы, а затем отправляет в него данные. Данные оцениваются с помощью обновленного файла `score.py`:

> [!IMPORTANT]
> Метод `reload` доступен только для локальных развертываний. Сведения об обновлении развертывания на другом целевом объекте вычислений см. в разделе "обновление" статьи [Развертывание моделей](how-to-deploy-and-where.md#update).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Скрипт перезагружается из расположения, указанного `InferenceConfig` объектом, используемым службой.

Чтобы изменить модель, Conda зависимости или конфигурацию развертывания, используйте [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). В следующем примере обновляется модель, используемая службой:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Удаление службы

Чтобы удалить службу, используйте [Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a id="dockerlog"></a>Проверка журнала DOCKER

Можно распечатать подробные сообщения журнала ядра Docker из объекта службы. Вы можете просмотреть журнал для ACI, AKS и локальных развертываний. В следующем примере показано, как напечатать журналы.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Сбой запуска службы

После успешной сборки образа система пытается запустить контейнер с помощью конфигурации развертывания. В ходе процесса запуска контейнера системой вызывается функция `init()` в скрипте оценки. При наличии неперехваченных исключений в функции `init()` в сообщении об ошибке может появиться ошибка **CrashLoopBackOff**.

Используйте сведения в разделе [Проверка журнала DOCKER](#dockerlog) для проверки журналов.

## <a name="function-fails-get_model_path"></a>Ошибка выполнения функции: get_model_path()

Часто в функции `init()` в скрипте оценки вызывается функция [model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) для определения местоположения файла модели или папки с файлами модели в контейнере. Если не удается найти файл или папку модели, происходит сбой функции. Самый простой способ устранить эту ошибку — это выполнить приведенный ниже код Python в оболочке контейнера.

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

В этом примере выводится локальный путь (относительно `/var/azureml-app`) в контейнере, в котором сценарий оценки ожидает, чтобы найти файл или папку модели. Затем можно проверить, действительно ли файл или папка находится там, где нужно.

Задание уровня ведения журнала отладки может привести к регистрации дополнительных сведений, что может оказаться полезным при определении сбоя.

## <a name="function-fails-runinput_data"></a>Ошибка выполнения функции: run(input_data)

Если служба успешно развернута, но аварийно завершает работу при публикации данных в конечную точку оценки, можно добавить оператор для перехвата ошибок в функцию `run(input_data)`, чтобы она возвращала подробное сообщение об ошибке. Пример.

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**Примечание**. Возврат сообщений об ошибках из вызова `run(input_data)` следует выполнять только в целях отладки. По соображениям безопасности не следует возвращать сообщения об ошибках в рабочей среде.

## <a name="http-status-code-503"></a>Код состояния HTTP 503

Развертывания службы Kubernetes Azure поддерживают Автомасштабирование, что позволяет добавлять реплики для поддержки дополнительной нагрузки. Однако Автомасштабирование предназначено для выполнения **постепенного** изменения нагрузки. При получении большого количества пиковых нагрузок в запросах в секунду клиенты могут получить код состояния HTTP 503.

Существует две вещи, которые помогут предотвратить коды состояния 503.

* Изменение уровня использования, при котором автоматическое масштабирование создает новые реплики.
    
    По умолчанию для автомасштабирования используется значение 70%. Это означает, что служба может обслуживать пиковые значения в запросах в секунду (RPS) до 30%. Цель использования можно настроить, задав для `autoscale_target_utilization` меньшее значение.

    > [!IMPORTANT]
    > Это изменение не приводит к тому, что реплики будут создаваться *быстрее*. Вместо этого они создаются с меньшим пороговым значением использования. Вместо того чтобы ждать, пока служба не будет использоваться в 70%, изменение значения на 30% приведет к тому, что реплики будут созданы при наступлении 30%.
    
    Если веб-служба уже использует текущие максимальные реплики и вы по-прежнему видите коды состояния 503, увеличьте значение `autoscale_max_replicas`, чтобы увеличить максимальное число реплик.

* Измените минимальное число реплик. Увеличение минимальных реплик предоставляет пул большего размера для обработки входящих пиков.

    Чтобы увеличить минимальное число реплик, задайте для `autoscale_min_replicas` более высокое значение. Необходимые реплики можно вычислить с помощью следующего кода, заменив значения значениями, характерными для вашего проекта:

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

    > [!NOTE]
    > Если вы получаете пиковые всплески запросов, превышающие количество новых минимальных реплик, вы можете получить 503s еще раз. Например, при увеличении трафика к службе может потребоваться увеличить минимальное количество реплик.

Дополнительные сведения о настройке `autoscale_target_utilization`, `autoscale_max_replicas`и `autoscale_min_replicas` для см. в справочнике по модулю [аксвебсервице](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) .


## <a name="advanced-debugging"></a>Усовершенствованная отладка

В некоторых случаях может потребоваться интерактивная отладка кода Python, содержащегося в развертывании модели. Например, если сценарий записи завершается ошибкой и причина не может быть определена дополнительным ведением журнала. С помощью Visual Studio Code и Инструменты Python для Visual Studio (PTVSD) можно присоединяться к коду, выполняющемуся в контейнере DOCKER.

> [!IMPORTANT]
> Этот метод отладки не работает при использовании `Model.deploy()` и `LocalWebservice.deploy_configuration` для локального развертывания модели. Вместо этого необходимо создать образ с помощью класса [контаинеримаже](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) . 

Для локальных развертываний веб-служб требуется работающая установка DOCKER в локальной системе. Дополнительные сведения об использовании DOCKER см. в [документации по DOCKER](https://docs.docker.com/).

### <a name="configure-development-environment"></a>Настройка среды разработки

1. Чтобы установить Инструменты Python для Visual Studio (PTVSD) в локальной среде разработки VS Code, используйте следующую команду:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Дополнительные сведения об использовании PTVSD с VS Code см. в разделе [Удаленная отладка](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Чтобы настроить VS Code для взаимодействия с образом DOCKER, создайте новую конфигурацию отладки:

    1. В VS Code откройте меню __Отладка__ и выберите пункт __Открыть конфигурации__. Откроется файл с именем __Launch. JSON__ .

    1. В файле __Launch. JSON__ найдите строку, содержащую `"configurations": [`, и вставьте следующий текст после него:

        ```json
        {
            "name": "Azure Machine Learning: Docker Debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "localhost",
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Если в разделе конфигурации уже есть другие записи, добавьте запятую (,) после вставленного кода.

        Этот раздел подключается к контейнеру DOCKER с помощью порта 5678.

    1. Сохраните файл __Launch. JSON__ .

### <a name="create-an-image-that-includes-ptvsd"></a>Создание образа, включающего PTVSD

1. Измените среду conda для развертывания, чтобы она включала PTVSD. В следующем примере показано, как добавить его с помощью параметра `pip_packages`.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 
    
    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.17', 'ptvsd'])
    
    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Чтобы запустить PTVSD и дождаться подключения при запуске службы, добавьте следующее в начало файла `score.py`:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Во время отладки может потребоваться внести изменения в файлы в образе без его повторного создания. Чтобы установить текстовый редактор (VIM) в образе DOCKER, создайте новый текстовый файл с именем `Dockerfile.steps` и используйте в качестве содержимого файла следующее:

    ```text
    RUN apt-get update && apt-get -y install vim
    ```

    Текстовый редактор позволяет изменять файлы в образе DOCKER для тестирования изменений без создания нового образа.

1. Чтобы создать образ, использующий файл `Dockerfile.steps`, используйте параметр `docker_file` при создании образа. В следующем примере показано, как это сделать:

    > [!NOTE]
    > В этом примере предполагается, что `ws` указывает на рабочую область Машинное обучение Azure и `model` является развертываемой моделью. Файл `myenv.yml` содержит зависимости conda, созданные на шаге 1.

    ```python
    from azureml.core.image import Image, ContainerImage
    image_config = ContainerImage.image_configuration(runtime= "python",
                                 execution_script="score.py",
                                 conda_file="myenv.yml",
                                 docker_file="Dockerfile.steps")

    image = Image.create(name = "myimage",
                     models = [model],
                     image_config = image_config, 
                     workspace = ws)
    # Print the location of the image in the repository
    print(image.image_location)
    ```

После создания образа в реестре отображается расположение образа. Расположение аналогично приведенному ниже тексту.

```text
myregistry.azurecr.io/myimage:1
```

В этом текстовом примере имя реестра — `myregistry` и образ называется `myimage`. Версия образа — `1`.

### <a name="download-the-image"></a>Загрузка образа

1. Откройте командную строку, терминал или другую оболочку и выполните следующую команду [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) для проверки подлинности в подписке Azure, содержащей рабочую область машинное обучение Azure:

    ```azurecli
    az login
    ```

1. Чтобы пройти проверку подлинности в реестре контейнеров Azure (запись контроля доступа), который содержит образ, используйте следующую команду. Замените `myregistry`, возвращенный при регистрации образа:

    ```azurecli
    az acr login --name myregistry
    ```

1. Чтобы скачать образ на локальный DOCKER, используйте следующую команду. Замените `myimagepath` расположением, возвращенным при регистрации образа:

    ```bash
    docker pull myimagepath
    ```

    Путь к образу должен быть похож на `myregistry.azurecr.io/myimage:1`. Где `myregistry` — это ваш реестр, `myimage` является вашим образом, а `1` — версией образа.

    > [!TIP]
    > Проверка подлинности из предыдущего шага не ограничена последними. Если вы ожидаете достаточно долго между командой проверки подлинности и командой Pull, вы получите ошибку проверки подлинности. В этом случае следует повторно пройти проверку подлинности.

    Время, необходимое для завершения загрузки, зависит от скорости подключения к Интернету. Во время процесса отображается состояние загрузки. После завершения загрузки можно использовать команду `docker images`, чтобы убедиться, что она загружена.

1. Чтобы упростить работу с изображением, используйте следующую команду, чтобы добавить тег. Замените `myimagepath` значением Location из шага 2.

    ```bash
    docker tag myimagepath debug:1
    ```

    В остальных шагах можно ссылаться на локальный образ как на `debug:1`, а не на полное значение пути к образу.

### <a name="debug-the-service"></a>Отладка службы

> [!TIP]
> Если для подключения PTVSD задано время ожидания в файле `score.py`, необходимо подключить VS Code к сеансу отладки до истечения времени ожидания. Запустите VS Code, откройте локальную копию `score.py`, установите точку останова и перед тем, как выполнить действия, описанные в этом разделе, прежде чем приступить к работе.
>
> Дополнительные сведения об отладке и установке точек останова см. в разделе [Отладка](https://code.visualstudio.com/Docs/editor/debugging).

1. Чтобы запустить контейнер DOCKER с помощью образа, используйте следующую команду:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Чтобы присоединить VS Code к PTVSD в контейнере, откройте VS Code и используйте клавишу F5 или выберите __Отладка__. При появлении запроса выберите __машинное обучение Azure: конфигурация отладки DOCKER__ . Можно также выбрать значок отладки на боковой панели, в раскрывающемся меню Отладка __машинное обучение Azure: DOCKER Debug__ , а затем с помощью зеленой стрелки присоединить отладчик.

    ![Значок "Отладка", кнопка "начать отладку" и селектор конфигурации](media/how-to-troubleshoot-deployment/start-debugging.png)

На этом этапе VS Code подключается к PTVSD в контейнере DOCKER и останавливается в точке останова, заданной ранее. Теперь можно выполнять код по мере выполнения, просматривать переменные и т. д.

Дополнительные сведения об использовании VS Code для отладки Python см. в разделе [Отладка кода Python](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Изменение файлов контейнеров

Чтобы внести изменения в файлы в образе, можно присоединиться к выполняющемуся контейнеру и выполнить оболочку Bash. В нем можно использовать Vim для изменения файлов:

1. Чтобы подключиться к выполняющемуся контейнеру и запустить интерпретатор bash в контейнере, используйте следующую команду:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Чтобы найти файлы, используемые службой, используйте следующую команду оболочки bash в контейнере, если каталог по умолчанию отличается от `/var/azureml-app`:

    ```bash
    cd /var/azureml-app
    ```

    Здесь можно использовать Vim для редактирования файла `score.py`. Дополнительные сведения об использовании vim см. [в разделе Использование редактора vim](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html).

1. Изменения в контейнере обычно не сохраняются. Чтобы сохранить внесенные изменения, используйте следующую команду перед выходом из оболочки, запущенной на предыдущем шаге (то есть в другой оболочке):

    ```bash
    docker commit debug debug:2
    ```

    Эта команда создает новый образ с именем `debug:2`, содержащий изменения.

    > [!TIP]
    > Прежде чем изменения вступят в силу, необходимо будет прерывать текущий контейнер и использовать новую версию.

1. Не забудьте внести изменения, внесенные в файлы в контейнере, в Sync с локальными файлами, которые VS Code использует. В противном случае работа отладчика не будет работать должным образом.

### <a name="stop-the-container"></a>Остановка контейнера

Чтобы прерывать контейнер, используйте следующую команду:

```bash
docker stop debug
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о развертывании см. в статьях, представленных ниже.

* [Развертывание моделей с помощью Службы машинного обучения Azure](how-to-deploy-and-where.md)
* [Учебник. Обучение & развертывании моделей](tutorial-train-models-with-aml.md)
