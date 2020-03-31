---
title: Руководство по устранению неполадок с развертыванием
titleSuffix: Azure Machine Learning
description: Узнайте, как обойти, решить и устранить распространенные ошибки развертывания Docker с помощью службы Azure Kubernetes и экземпляров контейнеров Azure с помощью Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: fab46f7d7ae74ad643ce3f122b27b0dc767f5a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399686"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Устранение проблем ныхлапов службы Azure Kubernetes и развертывания контейнерных экземпляров Azure

Узнайте, как обойти или решить распространенные ошибки развертывания Docker с azure Container Instances (ACI) и службой Azure Kubernetes (AKS) с помощью Azure Machine Learning.

При развертывании модели в Машинном обучении Azure система выполняет ряд задач.

Рекомендуемый и самый современный подход к развертыванию модели — через [API Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) с использованием объекта [среды](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) в качестве параметра ввода. В этом случае наша служба создаст базовое изображение докера для вас на этапе развертывания и установит необходимые модели в один вызов. Основными задачами развертывания являются:

1. Регистрация модели в реестре моделей рабочей области.

2. Определение конфигурации выводов:
    1. Создайте объект [среды](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) на основе зависимостей, указанных в файле yaml, или используйте одну из наших закупленных сред.
    2. Создайте конфигурацию выводов (объект InferenceConfig) на основе среды и сценария скоринга.

3. Развертывание модели в службе Azure Container Instance (ACI) или в службу Azure Kubernetes (AKS).

Дополнительные сведения об этом процессе см. во введении в [Управление моделями](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure**. Если у вас его нет, попробуйте [бесплатную или платную версию Azure Machine Learning.](https://aka.ms/AMLFree)
* [Azure машинного обучения SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* [Лазурный CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Расширение CLI для машинного обучения Azure.](reference-azure-machine-learning-cli.md)
* Чтобы отладить локально, вы должны иметь рабочую установку Docker на локальной системе.

    Чтобы проверить установку Docker, `docker run hello-world` используйте команду из терминала или запрос команды. Для получения информации об установке Docker или [Docker Documentation](https://docs.docker.com/)устранении ошибок Docker см.

## <a name="before-you-begin"></a>Перед началом

Если возникнут какие-либо проблемы, прежде всего следует разбить задачу развертывания (описанную ранее) на отдельные шаги, чтобы установить причину проблемы.

Предполагая, что вы используете новый/рекомендуемый метод развертывания через [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API с объектом [среды](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) в качестве ввода параметра, ваш код можно разбить на три основных шага:

1. Регистрация модели. Вот несколько примеров кода:

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Определение конфигурации выводов для развертывания:

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. Развертывание модели с помощью конфигурации выводов, созданной на предыдущем этапе:

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

Если возникли проблемы с развертыванием модели в ACI или AKS, попробуйте развернуть ее в качестве локального веб-сервиса. Использование локального веб-сервиса упрощает устранение неполадок. Изображение Docker, содержащее модель, загружается и запускается в локальной системе.

> [!WARNING]
> Локальные развертывания веб-служб не поддерживаются для сценариев производства.

Чтобы локально развернуться, измените код для создания `LocalWebservice.deploy_configuration()` конфигурации развертывания. Затем `Model.deploy()` используйте для развертывания службы. В следующем примере модель (содержащаяся в переменной модели) в качестве локального веб-сервиса:

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

Пожалуйста, обратите внимание, что если вы определяете свою собственную спецификацию conda YAML, вы должны перечислить лазурные по умолчанию с версией >1,0,45 в качестве зависимости от пипсов. Этот пакет содержит функции, необходимые для размещения модели в качестве веб-службы.

На этом этапе вы можете работать с службой в обычном режиме. Например, в следующем коде показано отправку данных в службу:

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

Для получения дополнительной информации о настройке среды Python [см.](how-to-use-environments.md) 

### <a name="update-the-service"></a>Обновление службы

Во время локального тестирования `score.py` может потребоваться обновить файл для добавления журналов или попытки устранить обнаруженные проблемы. Для перезагрузки изменений в файле `score.py` используйте. `reload()` Например, следующий код перезагружает скрипт для службы, а затем отправляет данные в него. Данные забрасываются с помощью обновленного `score.py` файла:

> [!IMPORTANT]
> Метод `reload` доступен только для локальных развертываний. Для получения [информации](how-to-deploy-and-where.md#update)об обновлении развертывания в другой вычислительной цели см.

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Скрипт перезагружается из местоположения, указанного `InferenceConfig` объектом, используемым службой.

Чтобы изменить модель, зависимости Conda или конфигурацию развертывания, используйте [обновление()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-) Следующий пример обновляет модель, используемую службой:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Удаление службы

Чтобы удалить службу, используйте [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a>Осмотрите журнал Docker

Можно распечатать подробные сообщения журнала ядра Docker из объекта службы. Можно просмотреть журнал для ACI, AKS и локальных развертываний. В следующем примере показано, как печатать журналы.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Сбой запуска службы

После успешного построения изображения система пытается запустить контейнер с помощью конфигурации развертывания. В ходе процесса запуска контейнера системой вызывается функция `init()` в скрипте оценки. При наличии неперехваченных исключений в функции `init()` в сообщении об ошибке может появиться ошибка **CrashLoopBackOff**.

Используйте информацию в разделе [Inspect the Docker](#dockerlog) для проверки журналов.

## <a name="function-fails-get_model_path"></a>Ошибка выполнения функции: get_model_path()

Часто в `init()` функции в скрипте скоринга вызывается функция [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) для поиска файла модели или папки файлов модели в контейнере. Если файл модели или папка не могут быть найдены, функция выходит из строя. Самый простой способ устранить эту ошибку — это выполнить приведенный ниже код Python в оболочке контейнера.

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Этот пример печатает локальный путь `/var/azureml-app`(относительно ) в контейнере, где ваш скоринговый скрипт ожидает найти файл модели или папку. Затем можно проверить, действительно ли файл или папка находится там, где нужно.

Установка уровня регистрации на DEBUG может привести к регистрации дополнительной информации, что может быть полезно для выявления сбоя.

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

**Примечание**. Возврат сообщений об ошибках из вызова `run(input_data)` следует выполнять только в целях отладки. По соображениям безопасности не следует возвращать сообщения об ошибках таким образом в производственной среде.

## <a name="http-status-code-502"></a>Код статуса HTTP 502

Код состояния 502 указывает на то, что служба `run()` вбросила исключение или разбила метод score.py файла. Используйте информацию в этой статье для отладки файла.

## <a name="http-status-code-503"></a>Код статуса HTTP 503

Развертывание службы Azure Kubernetes поддерживает автоматическое масштабирование, что позволяет добавлять реплики для поддержки дополнительной нагрузки. Тем не менее, autoscaler предназначен для обработки **постепенных** изменений в нагрузке. Если вы получаете большие пики запросов в секунду, клиенты могут получить код статуса HTTP 503.

Есть две вещи, которые могут помочь предотвратить 503 коды статуса:

* Измените уровень использования, на котором автомасштабирование создает новые реплики.
    
    По умолчанию использование целевых запросов автоматического масштабирования установлено на 70%, что означает, что служба может обрабатывать пики запросов в секунду (RPS) до 30%. Цель использования можно настроить, установив `autoscale_target_utilization` его на более низкое значение.

    > [!IMPORTANT]
    > Это изменение не приводит к *тому,* что реплики создаются быстрее. Вместо этого они создаются с более низким порогом использования. Вместо того, чтобы ждать, пока услуга 70% используется, изменение значения до 30% вызывает реплики, которые будут созданы, когда 30% использование происходит.
    
    Если веб-служба уже использует текущие реплики max и вы все еще `autoscale_max_replicas` видите 503 кода состояния, увеличьте значение, чтобы увеличить максимальное количество реплик.

* Измените минимальное количество реплик. Увеличение минимальных реплик обеспечивает больший пул для обработки входящих шипов.

    Чтобы увеличить минимальное количество реплик, установите `autoscale_min_replicas` более высокое значение. Вычислить требуемые реплики можно с помощью следующего кода, заменив значения значениями, определенными для проекта:

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
    > Если вы получаете пики запроса больше, чем новые минимальные реплики могут обрабатывать, вы можете получить 503s снова. Например, по мере увеличения трафика на службу может потребоваться увеличить минимальные реплики.

Для получения дополнительной `autoscale_target_utilization` `autoscale_max_replicas`информации `autoscale_min_replicas` о настройке, и для, [см.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py)

## <a name="http-status-code-504"></a>Код статуса HTTP 504

Код состояния 504 указывает, что запрос приурочен. Тайм-аут по умолчанию составляет 1 минуту.

Можно увеличить тайм-аут или ускорить обслуживание, изменив score.py для удаления ненужных вызовов. Если эти действия не исправляют проблему, используйте информацию в этой статье для отладки score.py файла. Код может находиться в зависшем состоянии или бесконечном цикле.

## <a name="advanced-debugging"></a>Расширенная отладка

В некоторых случаях может потребоваться интерактивнооте отладка кода Python, содержащегося в развертывании модели. Например, если скрипт входа не работает и причина не может быть определена дополнительными лесозаготовками. Используя Visual Studio Code и Python Tools for Visual Studio (PTVSD), можно прикрепить код, работающий внутри контейнера Docker.

> [!IMPORTANT]
> Этот метод отладки не `Model.deploy()` работает `LocalWebservice.deploy_configuration` при использовании и развертывании модели локально. Вместо этого необходимо создать изображение с помощью метода [Model.package()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-)

Локальные развертывания веб-служб требуют рабочей установки Docker в локальной системе. Для получения дополнительной информации об [использовании](https://docs.docker.com/)Docker см.

### <a name="configure-development-environment"></a>Настройка среды разработки

1. Для установки Python Tools for Visual Studio (PTVSD) в местную среду разработки VS Code используйте следующую команду:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Для получения дополнительной информации об использовании PTVSD с VS Code [см.](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)

1. Для настройки VS Code для связи с изображением Docker создайте новую конфигурацию отладки:

    1. Из VS Code выберите меню __Debug,__ а затем выберите __открытые конфигурации.__ Открывается файл под названием __launch.json.__

    1. В файле __launch.json__ найдите `"configurations": [`строку, содержащую ее, и вставьте следующий текст после нее:

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
        > Если в разделе конфигураций уже есть другие записи, добавьте запятую ()) после вставленного кода.

        Этот раздел прикрепляется к контейнеру Docker с помощью порта 5678.

    1. Сохранить файл __launch.json.__

### <a name="create-an-image-that-includes-ptvsd"></a>Создание изображения, включающий PTVSD

1. Измените среду conda для развертывания таким образом, чтобы она включала PTVSD. Следующий пример демонстрирует добавление `pip_packages` его с помощью параметра:

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

1. Чтобы запустить PTVSD и дождаться подключения, когда служба запускается, `score.py` добавьте следующее в верхней части файла:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Создайте изображение на основе определения среды и потяните изображение в локальный реестр. Во время отладки может потребоваться внести изменения в файлы изображения без необходимости его воссоздания. Для установки текстового редактора (vim) `Environment.docker.base_image` в `Environment.docker.base_dockerfile` изображении Docker используйте свойства:

    > [!NOTE]
    > Этот пример предполагает, что `ws` указывается на рабочее `model` пространство azure Machine Learning, а именно на развертываемую модель. Файл `myenv.yml` содержит зависимости conda, созданные в шаге 1.

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

    После создания и загрузки изображения путь изображения (включает репозиторий, имя и тег, который в данном случае также является его дайджестом) отображается в сообщении, похожем на следующее:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Чтобы облегчить работу с изображением, используйте следующую команду, чтобы добавить тег. Заменить `myimagepath` значение местоположения с предыдущего шага.

    ```bash
    docker tag myimagepath debug:1
    ```

    Для остальных шагов можно отнести к локальному изображению как `debug:1` к полному значению траектории изображения.

### <a name="debug-the-service"></a>Отладить службу

> [!TIP]
> Если вы установили тайм-аут для подключения `score.py` PTVSD в файле, необходимо подключить VS Code к сеансу отладки до истечения тайм-аута. Запустите VS Code, откройте локальную копию, `score.py`установите точку разрыва и приготовьте ее к работе перед использованием шагов в этом разделе.
>
> Для получения дополнительной информации о отладке и настройке точек разрыва [см.](https://code.visualstudio.com/Docs/editor/debugging)

1. Для запуска контейнера Docker с помощью изображения используйте следующую команду:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Чтобы прикрепить VS Code к PTVSD внутри контейнера, откройте VS Code и используйте ключ F5 или выберите __Debug.__ При запросе выберите конфигурацию __Azure Machine Learning: Docker Debug.__ Вы также можете выбрать значок отладки из боковой панели, __Azure Machine Learning: Docker Отладка__ запись из меню отладки, а затем использовать зеленую стрелку, чтобы прикрепить отладчика.

    ![Значок отладки, кнопка отладки и селектор конфигурации](./media/how-to-troubleshoot-deployment/start-debugging.png)

На этом этапе VS Code подключается к PTVSD внутри контейнера Docker и останавливается в точке разрыва, установленной ранее. Теперь вы можете пройти через код, как он работает, просматривать переменные и т.д.

Для получения дополнительной информации об использовании [Debug your Python code](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)VS Code для отладки Python см.

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Изменение файлов контейнеров

Чтобы внести изменения в файлы на изображении, можно прикрепить к бегущей таре и выполнить оболочку Bash. Оттуда вы можете использовать vim для отсеивания файлов:

1. Чтобы подключиться к бегущей таре и запустить оболочку Bash в контейнере, используйте следующую команду:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Чтобы найти файлы, используемые службой, используйте следующую команду из оболочки `/var/azureml-app`Bash в контейнере, если каталог по умолчанию отличается от:

    ```bash
    cd /var/azureml-app
    ```

    Отсюда вы можете использовать vim `score.py` для отсеивательного файла. Для получения дополнительной информации об [использовании](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html)vim, см.

1. Изменения в контейнере обычно не сохраняются. Чтобы сохранить любые изменения, которые вы вносят, используйте следующую команду, прежде чем выйти оболочки начал асьг не выше (то есть в другой оболочке):

    ```bash
    docker commit debug debug:2
    ```

    Эта команда создает новое `debug:2` изображение с именем, содержащее ваши веки.

    > [!TIP]
    > Вам нужно будет остановить текущий контейнер и начать использовать новую версию, прежде чем изменения вступят в силу.

1. Убедитесь в том, чтобы изменения, внесенные в файлы в контейнере, синхронизировались с локальными файлами, которые использует VS Code. В противном случае опыт отладчика не будет работать, как ожидалось.

### <a name="stop-the-container"></a>Остановка контейнера

Чтобы остановить контейнер, используйте следующую команду:

```bash
docker stop debug
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о развертывании см. в статьях, представленных ниже.

* [Развертывание моделей с помощью Службы машинного обучения Azure](how-to-deploy-and-where.md)
* [Учебник: Поезд & развертывать модели](tutorial-train-models-with-aml.md)
