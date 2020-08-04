---
title: Устранение неполадок при развертывании DOCKER
titleSuffix: Azure Machine Learning
description: Узнайте, как обойти и устранить распространенные ошибки развертывания DOCKER с помощью службы Kubernetes Azure и экземпляров контейнеров Azure, используя Машинное обучение Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 03/05/2020
ms.topic: conceptual
ms.custom: troubleshooting, contperfq4, tracking-python
ms.openlocfilehash: 4741c6348c2a4077776d2d79bee56de26f62e2d1
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87540943"
---
# <a name="troubleshoot-docker-deployment-of-models-with-azure-kubernetes-service-and-azure-container-instances"></a>Устранение неполадок при развертывании в DOCKER моделей с помощью службы Kubernetes Azure и экземпляров контейнеров Azure 

Узнайте, как устранить и устранить распространенные ошибки развертывания DOCKER в службе "экземпляры контейнеров Azure" (ACI) и службу Kubernetes Azure (AKS) с помощью Машинное обучение Azure.

## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure**. Если у вас ее нет, используйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree).
* [Пакет SDK для Машинного обучения Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Расширение CLI для Машинного обучения Azure](reference-azure-machine-learning-cli.md).
* Для локальной отладки вам необходима рабочая установка Docker в локальной системе.

    Чтобы проверить установку Docker, выполните команду `docker run hello-world` в терминале или командной строке. Сведения об установке Docker или устранении ошибок Docker см. в [документации Docker](https://docs.docker.com/).

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Шаги для развертывания DOCKER в моделях машинного обучения

При развертывании модели в Машинном обучении Azure система выполняет ряд задач.

Рекомендуемый подход к развертыванию модели заключается в использовании API [модели. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) с использованием объекта [среды](how-to-use-environments.md) в качестве входного параметра. В этом случае служба создает базовый образ DOCKER на этапе развертывания и подключает все необходимые модели в одном вызове. Основные задачи развертывания:

1. Регистрация модели в реестре моделей рабочей области.

2. Определение конфигурации вывода:
    1. Создайте объект [среды](how-to-use-environments.md) на основе зависимостей, указанных в файле yaml среды, или используйте одну из приобретенных сред.
    2. Создайте конфигурацию вывода (объект InferenceConfig) на основе среды и сценария оценки.

3. Разверните модель в службе "Экземпляр контейнера Azure" (ACI) или в Службе Azure Kubernetes (AKS).

Дополнительные сведения об этом процессе см. во введении в [Управление моделями](concept-model-management-and-deployment.md).

## <a name="before-you-begin"></a>Перед началом

Если возникнут какие-либо проблемы, прежде всего следует разбить задачу развертывания (описанную ранее) на отдельные шаги, чтобы установить причину проблемы.

Так как предполагается, что вы используете новый/рекомендуемый метод развертывания (использование API [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) с объектом [среды](how-to-use-environments.md) в качестве входного параметра), ваш код можно разбить на три основных этапа:

1. Регистрация модели. Ниже приведен пример кода:

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Определение конфигурации вывода для развертывания:

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. Разверните модель, используя конфигурацию вывода, созданную на предыдущем шаге:

    ```python
    from azureml.core.webservice import AciWebservice


    # deploy the model
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)
    aci_service = Model.deploy(workspace=ws,
                           name='my-service',
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aci_config)
    aci_service.wait_for_deployment(show_output=True)
    ```

После разбиения процесса развертывания на отдельные задачи можно взглянуть на самые распространенные ошибки.

## <a name="debug-locally"></a>Отладка в локальной среде

Если при развертывании модели в ACI или AKS возникли проблемы, попробуйте развернуть ее как локальную веб-службу. Использование локальной веб-службы упрощает устранение неполадок. Образ Docker, содержащий модель, скачивается и запускается в локальной системе.

Пример [локального развертывания](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb) можно найти в репозитории [мачинелеарнингнотебукс](https://github.com/Azure/MachineLearningNotebooks) , чтобы просмотреть пример готового к запуску примера.

> [!WARNING]
> Развертывания локальных веб-служб не поддерживаются для рабочих сред.

Для развертывания в локальной среде измените код, чтобы использовать `LocalWebservice.deploy_configuration()` для создания конфигурации развертывания. Затем используйте `Model.deploy()` для развертывания службы. В следующем примере модель (содержащаяся в переменной моделей) развертывается как локальная веб-служба:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
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

Если вы определяете собственную спецификацию conda YAML, необходимо составить список azureml-Defaults с версией >= 1.0.45 в качестве зависимости PIP. Этот пакет содержит функции, необходимые для размещения модели в качестве веб-службы.

На этом этапе вы можете использовать службу обычным образом. Например, следующий код демонстрирует отправку данных в службу:

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

Дополнительные сведения о настройке среды Python см. на [этой странице](how-to-use-environments.md). 

### <a name="update-the-service"></a>Обновление службы

Во время локального тестирования, возможно, нужно будет обновить файл `score.py`, чтобы добавить ведение журнала или попытаться решить любые обнаруженные проблемы. Чтобы перезагрузить изменения в файл `score.py`, используйте `reload()`. Например, следующий код перезагружает сценарий для службы, а затем отправляет в нее данные. Данные оцениваются с помощью обновленного файла `score.py`:

> [!IMPORTANT]
> Метод `reload` доступен лишь для локальных развертываний. Сведения об обновлении развертывания на другом целевом объекте вычислений см. [в разделе Обновление веб-службы](how-to-deploy-update-web-service.md).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Сценарий перезагружается из расположения, указанного объектом `InferenceConfig`, используемым службой.

Чтобы изменить модель, зависимости Conda или конфигурацию развертывания, используйте метод [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). В следующем примере обновляется модель, используемая службой:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Удаление службы

Чтобы удалить службу, воспользуйтесь методом [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Проверка журнала Docker

Можно распечатать подробные сообщения журнала ядра Docker из объекта службы. Вы можете просмотреть журнал для ACI, AKS и локальных развертываний. В следующем примере показано, как вывести данные журналов.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```
Если строка `Booting worker with pid: <pid>` в журналах встречается несколько раз, это означает, что недостаточно памяти для запуска рабочей роли.
Эту ошибку можно устранить, увеличив значение `memory_gb` в`deployment_config`
 
## <a name="container-cannot-be-scheduled"></a>Контейнер невозможно запланировать

При развертывании службы в целевой объект вычислений Службы Azure Kubernetes Машинное обучение Azure попытается запланировать службу с запрошенным количеством ресурсов. Если через 5 минут в кластере нет доступных узлов с соответствующим объемом доступных ресурсов, развертывание завершится с сообщением `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` . Вы можете устранить эту ошибку, добавив больше узлов, изменив SKU своих узлов или изменив требования к ресурсам службы. 

Сообщение об ошибке обычно указывает, какой ресурс требуется больше, например, если отображается сообщение об ошибке, указывающее на то, `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` что для службы требуются GPU, а в кластере есть три узла, на которых нет доступных графических процессоров. Эту проблему можно решить, добавив больше узлов при использовании SKU-номера GPU. В противном случае переключитесь на SKU с поддержкой GPU или измените среду, убрав необходимость использования GPU.  

## <a name="service-launch-fails"></a>Сбой запуска службы

После успешной сборки образа система пытается запустить контейнер с использованием конфигурации развертывания. В ходе процесса запуска контейнера системой вызывается функция `init()` в скрипте оценки. При наличии неперехваченных исключений в функции `init()` в сообщении об ошибке может появиться ошибка **CrashLoopBackOff**.

Используя сведения из [этого раздела](#dockerlog), проверьте журналы.

## <a name="function-fails-get_model_path"></a>Ошибка выполнения функции: get_model_path()

Часто в рамках функции `init()` в скрипте оценки вызывается функция [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-), чтобы найти файл модели или папку с файлами модели в контейнере. Если файл или папку модели найти не удается, происходит сбой функции. Самый простой способ устранить эту ошибку — это выполнить приведенный ниже код Python в оболочке контейнера.

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Этот пример выводит локальный путь (относительно `/var/azureml-app`) в контейнер, где скрипт оценки ожидает найти файл модели или папку с файлами. Затем можно проверить, действительно ли файл или папка находится там, где нужно.

При установке уровня ведения журнала DEBUG (для отладки) в нем может регистрироваться дополнительная информация, которая может быть полезна для определения причины сбоя.

## <a name="function-fails-runinput_data"></a>Ошибка выполнения функции: run(input_data)

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

**Примечание.** Возврат сообщений об ошибках из вызова `run(input_data)` следует выполнять только в целях отладки. Из соображений безопасности сообщения об ошибках не нужно возвращать таким способом в рабочей среде.

## <a name="http-status-code-502"></a>Код состояния HTTP 502

Код состояния 502 указывает, что в службе создано исключение или в методе `run()` файла score.py произошла ошибка. Используйте сведения из этой статьи для отладки файла.

## <a name="http-status-code-503"></a>Код состояния HTTP 503

Развертывания Службы контейнеров Azure поддерживают автомасштабирование, что позволяет добавлять реплики для поддержки дополнительной загрузки. Однако автомасштабирование предназначено для обработки **постепенных** изменений загрузки. В случае высоких скачков в запросах в секунду клиенты могут получить код состояния HTTP 503.

Предотвратить появление кода состояния 503 можно двумя способами:

* Измените уровень использования, при котором автоматическое масштабирование создает новые реплики.
    
    По умолчанию для целевого использования автоматического масштабирования установлено значение 70 %, а это означает, что служба может обрабатывать пики запросов в секунду (RPS) до 30 %. Вы можете настроить целевое использование, задав для `autoscale_target_utilization` более низкое значение.

    > [!IMPORTANT]
    > Это изменение не *ускорит* создание реплик. Просто они будут создаваться с более низким порогом использования. Вместо того, чтобы ждать, пока служба использует реплики на 70 %, измените значения на 30 %. Таким образом реплики будут создаваться при использовании 30 %.
    
    Если веб-служба уже использует все текущие реплики, а код состояния 503 не исчезает, увеличьте значение `autoscale_max_replicas`, чтобы повысить максимальное количество реплик.

* Измените минимальное количество реплик. Увеличение минимального количества реплик обеспечивает больший пул для обработки входящих пиков.

    Чтобы увеличить минимальное количество реплик, задайте для `autoscale_min_replicas` более высокое значение. Вы можете рассчитать необходимое количество реплик с помощью следующего кода, указав необходимые для вашего проекта значения:

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
    > Если пики запроса будут превышать новое минимальное количество реплик, снова отобразится код 503. Например, по мере увеличения трафика, поступающего в вашу службу, может потребоваться увеличение минимального количества реплик.

Дополнительные сведения о настройке `autoscale_target_utilization`, `autoscale_max_replicas` и `autoscale_min_replicas` см. на [этой странице](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py).

## <a name="http-status-code-504"></a>Код состояния HTTP 504

Код состояния 504 указывает на истечение времени ожидания запроса. Время ожидания по умолчанию составляет 1 минуту.

Вы можете увеличить время ожидания или попытаться ускорить службу, изменив файл score.py для удаления ненужных вызовов. Если проблема не исчезнет, используйте сведения из этой статьи для отладки файла score.py. Код может находиться в неотвечающем состоянии или в бесконечном цикле.

## <a name="advanced-debugging"></a>Расширенная отладка

В некоторых случаях может потребоваться интерактивная отладка кода Python, содержащегося в развертывании модели. Например, если начальный сценарий не работает и причину невозможно определить с помощью дополнительного ведения журнала. Используя Visual Studio Code и Инструменты Python для Visual Studio (PTVSD), вы можете присоединить отладчик к коду, выполняющемуся внутри контейнера Docker.

> [!IMPORTANT]
> Этот метод отладки не работает при использовании `Model.deploy()` и `LocalWebservice.deploy_configuration` для развертывания модели в локальной среде. В этом случае необходимо создать образ, используя метод [Model.package()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-).

Для локальных развертываний веб-службы требуется рабочая установка Docker в локальной системе. Дополнительные сведения об использовании Docker см. в [соответствующей документации](https://docs.docker.com/).

### <a name="configure-development-environment"></a>Настройка среды разработки

1. Чтобы установить Инструменты Python для Visual Studio (PTVSD) в локальной среде разработки VS Code, используйте следующую команду:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Дополнительные сведения об использовании PTVSD в VS Code см. на [этой странице](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Чтобы настроить VS Code для взаимодействия с образом Docker, создайте новую конфигурацию отладки:

    1. В VS Code выберите меню __Отладка__, а затем щелкните __Открыть конфигурации__. Откроется файл с именем __launch.json__.

    1. В файле __launch.json__ найдите строку, содержащую `"configurations": [`, и вставьте после нее следующий текст:

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

        Этот раздел присоединяется к контейнеру Docker через порт 5678.

    1. Сохраните файл __launch.json__.

### <a name="create-an-image-that-includes-ptvsd"></a>Создание образа, содержащего PTVSD

1. Измените среду conda для своего развертывания, добавив в нее PTVSD. В следующем примере демонстрируется добавление с помощью параметра `pip_packages`:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.45', 'ptvsd'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Чтобы запустить PTVSD и дождаться подключения при запуске службы, добавьте в начало своего файла `score.py` следующее:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Создайте образ на основе определения среды и извлеките его в локальный реестр. Во время отладки вы можете внести изменения в файлы образа, не создавая его повторно. Чтобы установить текстовый редактор (vim) в образе Docker, используйте свойства `Environment.docker.base_image` и `Environment.docker.base_dockerfile`:

    > [!NOTE]
    > В этом примере предполагается, что `ws` указывает на вашу рабочую область Машинное обучение Azure, а `model` представляет собой развертываемую модель. Файл `myenv.yml` содержит зависимости conda, созданные на шаге 1.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04\nRUN apt-get update && apt-get install vim -y"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    После создания и скачивания образа путь к нему (включая репозиторий, имя и тег, который в данном случае также является хэш-кодом) отображается в сообщении следующего вида:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Чтобы упростить работу с образом, используйте приведенную ниже команду для добавления тега. Замените `myimagepath` значением расположения из предыдущего шага.

    ```bash
    docker tag myimagepath debug:1
    ```

    На остальных шагах для обозначения расположения локального образа вы можете указывать `debug:1` вместо значения полного пути.

### <a name="debug-the-service"></a>Отладка службы

> [!TIP]
> Если вы установили время ожидания для подключения PTVSD в файле `score.py`, необходимо подключить VS Code к сеансу отладки до истечения этого времени. Запустите VS Code, откройте локальную копию `score.py`, установите точку останова и подготовьте ее к работе, прежде чем выполнять действия, описанные в этом разделе.
>
> Дополнительные сведения об отладке и установке точек останова см. на [этой странице](https://code.visualstudio.com/Docs/editor/debugging).

1. Чтобы запустить контейнер Docker с помощью образа, используйте следующую команду:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Чтобы присоединить VS Code к PTVSD внутри контейнера, откройте код VS Code и нажмите клавишу F5 или выберите __Отладка__. При появлении запроса выберите конфигурацию__Azure Machine Learning: Docker Debug__ (Машинное обучение Azure: отладка Docker). Вы также можете выбрать значок отладки на боковой панели, запись __Azure Machine Learning: Docker Debug__ (Машинное обучение Azure: отладка Docker), а затем щелкнуть зеленую стрелку, чтобы присоединить отладчик.

    ![Значок отладки, кнопка запуска отладки и селектор конфигурации](./media/how-to-troubleshoot-deployment/start-debugging.png)

На этом этапе VS Code подключается к PTVSD внутри контейнера Docker и останавливается при достижении точки останова, заданной ранее. Теперь вы можете пошагово выполнять код, просматривать переменные и т. д.

Дополнительные сведения об использовании VS Code для отладки Python см. на странице [Отладка кода Python](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Изменение файлов контейнера

Чтобы изменить файлы в образе, вы можете подключиться к работающему контейнеру и выполнить оболочку bash. В нем можно использовать vim для редактирования файлов:

1. Чтобы подключиться к работающему контейнеру и запустить оболочку bash в контейнере, используйте следующую команду:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Чтобы найти файлы, используемые службой, выполните приведенную ниже команду из оболочки bash в контейнере, если каталог по умолчанию отличается от `/var/azureml-app`:

    ```bash
    cd /var/azureml-app
    ```

    В нем можно использовать vim для редактирования файла `score.py`. Дополнительные сведения об использовании vim см. на [этой странице](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html).

1. Изменения в контейнере обычно не сохраняются. Чтобы сохранить внесенные изменения, используйте следующую команду перед выходом из оболочки, запущенной на шаге выше (то есть в другой оболочке):

    ```bash
    docker commit debug debug:2
    ```

    Эта команда создаст новый образ с именем `debug:2`, содержащий изменения.

    > [!TIP]
    > Вам нужно будет остановить текущий контейнер и начать использование новой версии, прежде чем изменения вступят в силу.

1. Обязательно синхронизируйте изменения, внесенные в файлы в контейнере, с локальными файлами, которые использует VS Code. В противном случае интерфейс отладчика не будет функционировать должным образом.

### <a name="stop-the-container"></a>Остановка контейнера

Чтобы остановить контейнер, используйте следующую команду:

```bash
docker stop debug
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о развертывании см. в статьях, представленных ниже.

* [Развертывание моделей с помощью Службы машинного обучения Azure](how-to-deploy-and-where.md)
* [Руководство. Обучение и развертывание моделей](tutorial-train-models-with-aml.md)
