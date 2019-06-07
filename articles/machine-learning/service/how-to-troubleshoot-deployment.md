---
title: Руководство по устранению неполадок с развертыванием
titleSuffix: Azure Machine Learning service
description: Узнайте, как обойти решать и устранение распространенных ошибок развертывания Docker с AKS и ACI, с помощью службы машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 05/02/2018
ms.custom: seodec18
ms.openlocfilehash: 9d173409fc9f270f13d688999a88f9480c3eb40f
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66742924"
---
# <a name="troubleshooting-azure-machine-learning-service-aks-and-aci-deployments"></a>Устранение неполадок при развертывании AKS и ACI с помощью Службы машинного обучения Azure

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
                                                      execution_script="score.py",
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


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о развертывании см. в статьях, представленных ниже.

* [Развертывание моделей с помощью Службы машинного обучения Azure](how-to-deploy-and-where.md)
* [Руководство. Обучение и развертывание моделей](tutorial-train-models-with-aml.md)
