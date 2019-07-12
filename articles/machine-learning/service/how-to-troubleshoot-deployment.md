---
title: Руководство по устранению неполадок с развертыванием
titleSuffix: Azure Machine Learning service
description: Узнайте, как обойти решать и устранение распространенных ошибок развертывания Docker с помощью службы Azure Kubernetes и экземпляры контейнеров Azure, с помощью службы машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 07/09/2018
ms.custom: seodec18
ms.openlocfilehash: e0f4b024d717c08df3514df057abf89d55be1dc9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707040"
---
# <a name="troubleshooting-azure-machine-learning-service-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Устранение неполадок при развертывании службы Azure Kubernetes и экземпляры контейнеров Azure службы машинного обучения Azure

Узнайте, как обойти или устранить распространенных ошибок развертывания в Docker с помощью экземпляров контейнеров Azure (ACI) и Azure Kubernetes Service (AKS) с помощью службы машинного обучения Azure.

При развертывании модели в службе машинного обучения Azure система выполняет ряд задач. Ниже перечислены задачи развертывания.

1. Регистрация модели в реестре моделей рабочей области.

2. Создание образа Docker, включая:
    1. загрузку зарегистрированной модели из реестра; 
    2. создание файла dockerfile в среде Python на основе зависимостей, указываемых в yaml-файле среды;
    3. добавление файлов модели и скрипта оценки, которые содержатся в файле dockerfile;
    4. создание нового образа Docker с помощью файла dockerfile;
    5. регистрацию образа Docker с помощью реестра контейнеров Azure, связанного с рабочей областью.

    > [!IMPORTANT]
    > В зависимости от кода создания образа происходит автоматически без входных данных.

3. Развертывание образа Docker в службу "Экземпляр контейнера Azure" (ACI) или службу Azure Kubernetes (AKS).

4. Запуск новых контейнеров в ACI или AKS. 

Дополнительные сведения об этом процессе см. во введении в [Управление моделями](concept-model-management-and-deployment.md).

## <a name="before-you-begin"></a>Перед началом работы

Если возникнут какие-либо проблемы, прежде всего следует разбить задачу развертывания (описанную ранее) на отдельные шаги, чтобы установить причину проблемы.

Формирование задачи развертывания полезна, если вы используете [Webservice.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) API, или [Webservice.deploy_from_model()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) API, как обе эти функции выполните действия, упомянутые выше, одним действием. Обычно эти API удобны, но он позволяет разбить действия при устранении неполадок, заменив их с помощью ниже вызовов API.

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

Если образ Docker не может быть построен, [image.wait_for_creation()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) или [service.wait_for_deployment()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-) вызов завершается неудачно с некоторые сообщения об ошибках, которые может предложить некоторые особенности. Более подробную информацию об ошибках также можно найти в журнале сборки образа. Ниже приведен пример кода, в котором отображено обнаружение кода URI журнала сборки образа.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print (img.name, img.version, img.image_build_log_uri)
```

Код URI журнала образа — это URL-адрес SAS, указывающий на файл журнала, сохраненный в хранилище BLOB-объектов Azure. Просто скопируйте код URI и вставьте его в окно браузера, и вы сможете загрузить и просмотреть файл журнала.

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Политика доступа к Azure Key Vault и шаблонов Azure Resource Manager

Сборка образа может также ошибкой из-за проблемы с политикой доступа в Azure Key Vault. Это может произойти при использовании шаблона Azure Resource Manager для создания рабочей области и связанных ресурсов (включая хранилище ключей Azure), несколько раз. Например с помощью шаблона несколько раз с теми же параметрами, как часть непрерывной интеграции и развертывания конвейера.

Большинство операций создания ресурсов через шаблоны являются идемпотентными, но очищает хранилище ключей, политики доступа каждый раз при использовании шаблона. Удаление политики разрывы доступ к Key Vault для любой существующей рабочей области, который ее использует. В результате ошибки при попытке создать новые образы. Ниже приведены примеры ошибок, которые могут получать:

__Портал__.
```text
Create image "myimage": An internal server error occurred. Please try again. If the problem persists, contact support.
```

__ПАКЕТ SDK__:
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

Чтобы избежать этой проблемы, рекомендуется один из следующих подходов:

* Не развертывайте шаблон более одного раза для те же параметры. Или удалить существующие ресурсы перед использованием шаблона, чтобы повторно создать их.
* Проверьте политики доступа хранилища ключей и затем использовать эти политики для задания `accessPolicies` свойства шаблона.
* Проверьте, существует ли уже ресурса хранилища ключей. В этом случае не создавайте его с помощью шаблона. Например добавьте параметр, который позволяет отключить создание ресурса хранилища ключей, если он уже существует.

## <a name="debug-locally"></a>Локальная отладка

Если возникли проблемы при развертывании модели в ACI и AKS, попробуйте развернуть его как локальный веб-службы. С помощью локального веб-служба облегчает Устранение неполадок. Образ Docker, содержащий модель загружается и работы в локальной системе.

> [!IMPORTANT]
> Развертывания локального веб-служб требуется рабочая установки Docker в локальной системе. Docker должна быть запущена перед развертыванием локальных веб-службы. Сведения об установке и использовании Docker см. в разделе [ https://www.docker.com/ ](https://www.docker.com/).

> [!WARNING]
> Развертывания локального веб-служб не поддерживаются для производственных сценариев.

Для локального развертывания, измените код, чтобы использовать `LocalWebservice.deploy_configuration()` для создания конфигурации развертывания. Затем с помощью `Model.deploy()` для развертывания службы. В следующем примере развертывается модель (содержится в `model` переменной) как локальные веб-службы:

```python
from azureml.core.model import InferenceConfig,Model
from azureml.core.webservice import LocalWebservice

# Create inference configuration. This creates a docker image that contains the model.
inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml")

# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

На этом этапе можно работать со службой в обычном режиме. Например следующий код демонстрирует отправку данных в службу:

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

### <a name="update-the-service"></a>Обновление службы

Во время локального тестирования, может потребоваться обновить `score.py` файл, чтобы добавить ведение журнала или попытайтесь устранить любые проблемы, которые вы ознакомились с. Чтобы перезагрузить изменения для `score.py` файла следует использовать `reload()`. Например следующий код перезагружает сценарий для службы и отправляет данные. Данные оцениваются с помощью обновленного `score.py` файла:

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Скрипт загружается из расположения, указанного `InferenceConfig` объект, используемый службой.

Чтобы изменить модель, с зависимостями Conda или конфигурации развертывания, используйте [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). В следующем примере обновляется модель, используемая службой:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Удалить службу

Чтобы удалить службу, используйте [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a id="dockerlog"></a> Проверьте журнал Docker

Можно распечатать подробные сообщения журнала ядра Docker из объекта службы. Можно просмотреть журнал для локальных развертываний, ACI и AKS. Следующий пример демонстрирует способ печати журналы.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Сбой запуска службы

После успешной сборки образа система пытается запустить контейнер с помощью конфигурации развертывания. В ходе процесса запуска контейнера системой вызывается функция `init()` в скрипте оценки. При наличии неперехваченных исключений в функции `init()` в сообщении об ошибке может появиться ошибка **CrashLoopBackOff**.

Используйте сведения в [Проверьте журнал Docker](#dockerlog) раздела, чтобы проверить журналы.

## <a name="function-fails-getmodelpath"></a>Ошибка выполнения функции: get_model_path()

Часто в `init()` функции в скрипт оценки, [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) функция вызывается, чтобы найти файл модели или папки с файлами модели в контейнере. Если не удается найти модель файла или папки, то функция завершается с ошибкой. Самый простой способ устранить эту ошибку — это выполнить приведенный ниже код Python в оболочке контейнера.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

В этом примере выводит локальный путь (относительно `/var/azureml-app`) в контейнере, где ваш скрипт оценки ожидает найти модель файла или папки. Затем можно проверить, действительно ли файл или папка находится там, где нужно.

Установка уровня ведения журнала отладки может привести Дополнительные сведения для записи, которые могут быть полезны для обнаружения сбоя.

## <a name="function-fails-runinputdata"></a>Ошибка выполнения функции: run(input_data)

Если служба успешно развернута, но аварийно завершает работу при публикации данных в конечную точку оценки, можно добавить оператор для перехвата ошибок в функцию `run(input_data)`, чтобы она возвращала подробное сообщение об ошибке. Пример:

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

**Примечание**. Возврат сообщений об ошибках из вызова `run(input_data)` следует выполнять только в целях отладки. По соображениям безопасности вы не должны возвращать сообщения об ошибках таким образом в рабочей среде.

## <a name="http-status-code-503"></a>Код состояния HTTP 503

Службы Kubernetes в развертываниях Azure поддерживается Автомасштабирование, которое обеспечивает реплик для добавления поддержки дополнительной нагрузки. Тем не менее, может обрабатывать автомасштабирования **постепенное** изменений нагрузки. Получив большим всплескам поступления запросов в секунду, клиенты могут получать код состояния HTTP 503.

Существует две вещи, которые могут помочь предотвратить 503 коды состояния:

* Изменить уровень загрузки, в которых Автомасштабирование создает новые реплики.
    
    По умолчанию имеет значение целевое использование автоматического масштабирования до 70%, означающее, что служба может обрабатывать пиковые нагрузки запросов в секунду (RPS) до 30%. Можно настроить целевого показателя использования, присвоив `autoscale_target_utilization` меньшее значение.

    > [!IMPORTANT]
    > Это изменение не приводит к реплик должен быть создан *быстрее*. Вместо этого они будут созданы в нижнего порогового значения использования. Не ждать, пока служба используется 70%, изменив значение на 30% вызывает реплик, создаваемого в случае использования 30%.
    
    Если веб-службы уже использует текущие max реплики и по-прежнему видите 503 коды состояния, увеличьте `autoscale_max_replicas` значение, чтобы увеличить максимальное число реплик.

* Измените минимальное число реплик. Увеличить минимальное реплик предоставляет больший пул для обработки входящих пиковых нагрузок.

    Чтобы увеличить минимальное число реплик, задайте `autoscale_min_replicas` более высокое значение. Требуется реплики можно вычислить с помощью приведенный ниже, заменив значения со значениями, принятыми в проект:

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
    > Если появится запрос пики большего размера, чем может обработать новые реплики минимальное, может появиться 503s еще раз. Например как трафик увеличения службы, может потребоваться увеличить минимальное реплик.

Дополнительные сведения о параметр `autoscale_target_utilization`, `autoscale_max_replicas`, и `autoscale_min_replicas` , см. в разделе [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) ссылка на модуль.


## <a name="advanced-debugging"></a>Усовершенствованная Отладка

В некоторых случаях может потребоваться интерактивную отладку кода Python, содержащихся в модели развертывания. Например, если происходит сбой сценарий входа и причина не может определяться дополнительное ведение журнала. С помощью Visual Studio Code, а также инструменты Python для Visual Studio (PTVSD), можно присоединить к код, выполняющийся в контейнере Docker.

> [!IMPORTANT]
> Этот метод отладки не работает при использовании `Model.deploy()` и `LocalWebservice.deploy_configuration` развертывание модели локально. Вместо этого необходимо создать образ с помощью [ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) класса. 
>
> Развертывания локального веб-служб требуется рабочая установки Docker в локальной системе. Docker должна быть запущена перед развертыванием локальных веб-службы. Сведения об установке и использовании Docker см. в разделе [ https://www.docker.com/ ](https://www.docker.com/).

### <a name="configure-development-environment"></a>Настройка среды разработки

1. Чтобы установить инструменты Python для Visual Studio (PTVSD) в локальной среде разработки Visual STUDIO Code, используйте следующую команду:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Дополнительные сведения об использовании PTVSD с VS Code см. в разделе [удаленной отладки](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Чтобы настроить VS Code для взаимодействия с образа Docker, создайте новую конфигурацию отладки:

    1. В VS Code выберите __Отладка__ меню и выберите __откройте конфигураций__. Файл с именем __launch.json__ открывает.

    1. В __launch.json__ файл, найдите строку, которая содержит `"configurations": [`и вставьте следующий текст после него:

        ```json
        {
            "name": "Azure Machine Learning service: Docker Debug",
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
        > Если уже есть другие записи в разделе конфигурации, добавьте запятую (,) после вставленного кода.

        В этом разделе присоединяет в контейнер Docker, с помощью открытый порт 5678.

    1. Сохранить __launch.json__ файла.

### <a name="create-an-image-that-includes-ptvsd"></a>Создать образ, содержащий PTVSD

1. Таким образом, чтобы он включал PTVSD, измените среду conda для развертывания. В следующем примере показано добавление его с помощью `pip_packages` параметр:

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

1. Чтобы начать PTVSD и ожидания соединения при запуске службы, добавьте следующее в верхнюю часть вашей `score.py` файла:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Во время отладки может потребоваться внести изменения без необходимости ее повторного создания файлов в образе. Для установки в текстовом редакторе (vim) в образ Docker, создайте новый текстовый файл с именем `Dockerfile.steps` и используйте следующую команду в качестве содержимого файла:

    ```text
    RUN apt-get update && apt-get -y install vim
    ```

    В текстовом редакторе позволяет изменять файлы, находящиеся внутри образа docker, чтобы проверить изменения, не создавая новый образ.

1. Чтобы создать образ, использующий `Dockerfile.steps` файла следует использовать `docker_file` параметр при создании образа. Следующий пример демонстрирует, как это сделать:

    > [!NOTE]
    > В этом примере предполагается, что `ws` указывает на рабочей области машинного обучения Azure и что `model` — это модель развертывания. `myenv.yml` Файл содержит зависимости conda, созданное на шаге 1.

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

После создания образа, отображается расположение образа в реестре. Расположение похож на следующий текст:

```text
myregistry.azurecr.io/myimage:1
```

В этом примере текст имени реестра используется `myregistry` и используется образ `myimage`. Версия образа `1`.

### <a name="download-the-image"></a>Скачивание образа

1. Откройте командную строку, терминал или другую оболочку и используйте следующую команду [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) команду для проверки подлинности в подписку Azure, которая содержит рабочую область машинного обучения Azure:

    ```azurecli
    az login
    ```

1. Для проверки подлинности в реестр контейнеров Azure (ACR), содержащий образ, используйте следующую команду. Замените `myregistry` с тем, которое возвращается, когда вы зарегистрировали изображения:

    ```azurecli
    az acr login --name myregistry
    ```

1. Чтобы загрузить образ для локального Docker, используйте следующую команду. Замените `myimagepath` с расположением, возвращается, когда вы зарегистрировали изображения:

    ```bash
    docker pull myimagepath
    ```

    Путь к изображению должен быть аналогичен `myregistry.azurecr.io/myimage:1`. Где `myregistry` — ваш реестр `myimage` — это образ, и `1` — это версия образа.

    > [!TIP]
    > Проверку подлинности на предыдущем шаге неограниченно долго не действует. Если вы подождет достаточно долго между команды проверки подлинности и по запросу, вы получите ошибку проверки подлинности. В этом случае проверку подлинности.

    Время, необходимое для завершения загрузки зависит от скорости подключения к Интернету. Состояние загрузки отображается во время процесса. После завершения загрузки можно использовать `docker images` команду, чтобы убедиться, что его загрузки.

1. Чтобы сделать проще работать с изображением, используйте следующую команду для добавления тега. Замените `myimagepath` с значение расположения из шага 2.

    ```bash
    docker tag myimagepath debug:1
    ```

    Остальные шаги, см. в локальный образ как `debug:1` вместо значения путь полного образа.

### <a name="debug-the-service"></a>Отладка службы

> [!TIP]
> Если вы установить время ожидания для соединения PTVSD в `score.py` файл, необходимо соединиться с VS Code до истечения времени ожидания сеанса отладки. Запустите VS Code, открыть локальную копию `score.py`, установите точку останова, и будет готова к работе, прежде чем использовать шаги в этом разделе.
>
> Дополнительные сведения об отладке и задание точек останова см. в разделе [Отладка](https://code.visualstudio.com/Docs/editor/debugging).

1. Чтобы запустить контейнер Docker, с помощью образа, используйте следующую команду:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Чтобы подключить отладчик VS Code PTVSD внутри контейнера, откройте VS Code и воспользоваться клавишей F5, ключевые, или выберите __Отладка__. При появлении запроса, выберите __службы машинного обучения Azure: Docker отладки__ конфигурации. Можно также выбрать значок "Отладка" на боковой панели __службы машинного обучения Azure: Docker отладки__ запись из раскрывающегося меню "Отладка", а затем используйте зеленую стрелку, чтобы присоединить отладчик.

    ![Значок "Отладка", отладки "Пуск" и выбора конфигурации](media/how-to-troubleshoot-deployment/start-debugging.png)

На этом этапе VS Code подключается к PTVSD в контейнере Docker и останавливается в точке останова, заданными ранее. Теперь можно выполнить пошаговую через код во время их выполнения, просматривать переменные и т. д.

Дополнительные сведения об использовании VS Code для отладки Python, см. в разделе [отладку кода Python](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Измените файлы контейнера

Чтобы внести изменения в файлы изображения, можно подключить к запущенному контейнеру и выполнить оболочку bash. После этого можно использовать vim для редактирования файлов:

1. Чтобы подключиться к запущенному контейнеру и запустите оболочку bash в контейнер, используйте следующую команду:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Чтобы найти файлы, используемые службой, используйте следующую команду из оболочки bash в контейнере:

    ```bash
    cd /var/azureml-app
    ```

    На этой странице можно использовать vim для редактирования `score.py` файл. Дополнительные сведения об использовании vim, см. в разделе [с помощью редактора Vim](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html).

1. Изменения в контейнер, обычно не сохраняются. Чтобы сохранить любые изменения, внесенные, используйте следующую команду, перед выходом из оболочки к работе на предыдущем шаге (то есть в другой оболочкой):

    ```bash
    docker commit debug debug:2
    ```

    Эта команда создает новый образ с именем `debug:2` , содержащий изменения.

    > [!TIP]
    > Необходимо остановить текущий контейнер и начать использовать новую версию, прежде чем изменения вступят в силу.

1. Не забудьте сохранить изменения, внесенные в файлы в контейнере синхронизацию локальных файлов, используемых в VS Code. В противном случае процесс отладчика не будет работать должным образом.

### <a name="stop-the-container"></a>Остановка контейнера

Чтобы остановить контейнер, используйте следующую команду:

```bash
docker stop debug
```

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о развертывании см. в статьях, представленных ниже.

* [Развертывание моделей с помощью Службы машинного обучения Azure](how-to-deploy-and-where.md)
* [Руководство. Обучение и развертывание моделей](tutorial-train-models-with-aml.md)
