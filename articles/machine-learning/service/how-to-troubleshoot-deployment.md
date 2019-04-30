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
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 815be7400e0a0560ace7e07b317aeb25c2feacd5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817417"
---
# <a name="troubleshooting-azure-machine-learning-service-aks-and-aci-deployments"></a>Устранение неполадок при развертывании AKS и ACI с помощью Службы машинного обучения Azure

Из этой статьи вы узнаете, как обойти или устранить распространенные проблемы развертывания Docker с Экземплярами контейнеров Azure (ACI) и Службой Azure Kubernetes (AKS) с помощью Службы машинного обучения Azure.

При развертывании модели в службе машинного обучения Azure система выполняет ряд задач. Это сложная последовательность событий, и иногда возникают проблемы. Ниже перечислены задачи развертывания.

1. Регистрация модели в реестре моделей рабочей области.

2. Создание образа Docker, включая:
    1. загрузку зарегистрированной модели из реестра; 
    2. создание файла dockerfile в среде Python на основе зависимостей, указываемых в yaml-файле среды;
    3. добавление файлов модели и скрипта оценки, которые содержатся в файле dockerfile;
    4. создание нового образа Docker с помощью файла dockerfile;
    5. регистрацию образа Docker с помощью реестра контейнеров Azure, связанного с рабочей областью.

3. Развертывание образа Docker в службу "Экземпляр контейнера Azure" (ACI) или службу Azure Kubernetes (AKS).

4. Запуск новых контейнеров в ACI или AKS. 

Дополнительные сведения об этом процессе см. во введении в [Управление моделями](concept-model-management-and-deployment.md).

## <a name="before-you-begin"></a>Перед началом работы

Если возникнут какие-либо проблемы, прежде всего следует разбить задачу развертывания (описанную ранее) на отдельные шаги, чтобы установить причину проблемы. 

Это может оказаться полезным, если вы используете `Webservice.deploy` API, или `Webservice.deploy_from_model` API, так как эти функции сгруппировать упомянутые выше шаги в рамках одной операции. Обычно эти API удобны, но он позволяет разбить действия при устранении неполадок, заменив их с помощью ниже вызовов API.

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
Если система не может создать образ Docker, вызов `image.wait_for_creation()` завершается сбоем, выдавая сообщения об ошибках, в которых могут быть предложены некоторые подсказки. Более подробную информацию об ошибках также можно найти в журнале сборки образа. Ниже приведен пример кода, в котором отображено обнаружение кода URI журнала сборки образа.

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

Большинство операций создания ресурсов через шаблоны являются идемпотентными, но очищает хранилище ключей, политики доступа каждый раз при использовании шаблона. Это нарушает логику доступа к Key Vault для любой существующей рабочей области, который ее использует. В результате ошибки при попытке создать новые образы. Ниже приведены примеры ошибок, которые могут получать:

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
* Проверьте политики доступа Key Vault и используйте его для задания `accessPolicies` свойства шаблона.
* Проверьте, существует ли уже ресурса хранилища ключей. В этом случае не создавайте его с помощью шаблона. Например добавьте параметр, который позволяет отключить создание ресурса хранилища ключей, если он уже существует.

## <a name="service-launch-fails"></a>Сбой запуска службы
После успешной сборки образа система пытается запустить контейнер в ACI или AKS в зависимости от конфигурации развертывания. Рекомендуется сначала ACI развертывания, так как это простое развертывание в одном контейнере. Таким образом затем можно исключить все связанные с AKS проблемы.

В ходе процесса запуска контейнера системой вызывается функция `init()` в скрипте оценки. При наличии неперехваченных исключений в функции `init()` в сообщении об ошибке может появиться ошибка **CrashLoopBackOff**. Ниже приведены некоторые советы по устранению этой проблемы.

### <a name="inspect-the-docker-log"></a>Проверка журнала Docker
Можно распечатать подробные сообщения журнала ядра Docker из объекта службы.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

### <a name="debug-the-docker-image-locally"></a>Локальная отладка образа Docker
Иногда журнал Docker не выдает достаточно информации о том, что пошло не так. Можно пойти дальше и извлечь созданный образ Docker, запустить локальный контейнер и провести отладку непосредственно внутри динамического контейнера в интерактивном режиме. Для запуска локального контейнера ядро Docker должно выполняться локально, а также работу значительно упростит наличие установленного [azure-cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Сначала необходимо определить расположение образа.

```python
# print image location
print(image.image_location)
```

Расположение образа имеет следующий формат: `<acr-name>.azurecr.io/<image-name>:<version-number>`, например `myworkpaceacr.azurecr.io/myimage:3`. 

Теперь перейдите к окну командной строки. Если azure-cli установлен, можно ввести следующие команды для входа в ACR (реестр контейнеров Azure), связанный с рабочей областью, в которой хранится образ. 

```sh
# log on to Azure first if you haven't done so before
$ az login

# make sure you set the right subscription in case you have access to multiple subscriptions
$ az account set -s <subscription_name_or_id>

# now let's log in to the workspace ACR
# note the acr-name is the domain name WITHOUT the ".azurecr.io" postfix
# e.g.: az acr login -n myworkpaceacr
$ az acr login -n <acr-name>
```
Если azure-cli не установлен, для входа в ACR можно использовать команду `docker login`. Но сначала нужно получить имя пользователя и пароль ACR с портала Azure.

После входа в ACR можно извлечь образ Docker и запустить контейнер локально, а затем запустить сеанс Bash для отладки с помощью команды `docker run`.

```sh
# note the image_id is <acr-name>.azurecr.io/<image-name>:<version-number>
# for example: myworkpaceacr.azurecr.io/myimage:3
$ docker run -it <image_id> /bin/bash
```

После запуска сеанса Bash в выполняющемся контейнере скрипты оценки можно найти в папке `/var/azureml-app`. Затем можно запустить сеанс Python, чтобы отладить скрипты оценки. 

```sh
# enter the directory where scoring scripts live
cd /var/azureml-app

# find what Python packages are installed in the python environment
pip freeze

# sanity-check on score.py
# you might want to edit the score.py to trigger init().
# as most of the errors happen in init() when you are trying to load the model.
python score.py
```
При необходимости использовать текстовый редактор для изменения скриптов установите Vim, Nano, Emacs или другой предпочитаемый редактор.

```sh
# update package index
apt-get update

# install a text editor of your choice
apt-get install vim
apt-get install nano
apt-get install emacs

# launch emacs (for example) to edit score.py
emacs score.py

# exit the container bash shell
exit
```

Также можно запустить веб-службу локально и направлять в нее трафик HTTP. Сервер Flask в контейнере Docker работает через порт 5001. Можно сопоставить сервер с любым доступным на хост-компьютере портом.
```sh
# you can find the scoring API at: http://localhost:8000/score
$ docker run -p 8000:5001 <image_id>
```

## <a name="function-fails-getmodelpath"></a>Ошибка выполнения функции: get_model_path()
Часто в рамках функции `init()` в скрипте оценки вызывается функция `Model.get_model_path()`, чтобы найти файл модели или папку с файлами модели в контейнере. Это часто становится источником ошибок, если не удается найти файл модели или папку с файлами. Самый простой способ устранить эту ошибку — это выполнить приведенный ниже код Python в оболочке контейнера.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

Он выводит локальный путь (относительно `/var/azureml-app`) в контейнер, где скрипт оценки ожидает найти файл модели или папку с файлами. Затем можно проверить, действительно ли файл или папка находится там, где нужно.

При установке уровня ведения журнала DEBUG (для отладки) в журнале может регистрироваться дополнительная информация, которая может быть полезна для определения причины сбоя.

## <a name="function-fails-runinputdata"></a>Ошибка выполнения функции: run(input_data)
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
**Примечание**. Возврат сообщений об ошибках из вызова `run(input_data)` следует выполнять только в целях отладки. Делать это в рабочей среде не рекомендуется из соображений безопасности.

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

Дополнительные сведения о параметр `autoscale_target_utilization`, `autoscale_max_replicas`, и `autoscale_min_replicas` , см. в разделе [AksWebservice](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) ссылка на модуль.


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о развертывании см. в статьях, представленных ниже. 
* [Развертывание моделей с помощью Службы машинного обучения Azure](how-to-deploy-and-where.md)

* [Руководство. Обучение и развертывание моделей](tutorial-train-models-with-aml.md)
