---
title: Руководство по устранению неполадок с развертыванием
titleSuffix: Azure Machine Learning service
description: Узнайте, как обойти, решить и устранить распространенные проблемы развертывания Docker с AKS и ACI с помощью Службы машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: haining
author: hning86
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6bd3bc86aa828ab28462de9d45f660889634cbd7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53100520"
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

Это особенно полезно в том случае, если вы используете API `Webservice.deploy` (или API `Webservice.deploy_from_model`), так как эти функции группируют упомянутые выше шаги в единое действие. Обычно эти API весьма удобны, но во время устранения неполадок разбить задачу на шаги легче, если заменить их на приведенные ниже вызовы API.

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
print(ws.images()['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images().items():
    print (img.name, img.version, img.image_build_log_uri)
```
Код URI журнала образа — это URL-адрес SAS, указывающий на файл журнала, сохраненный в хранилище BLOB-объектов Azure. Просто скопируйте код URI и вставьте его в окно браузера, и вы сможете загрузить и просмотреть файл журнала.


## <a name="service-launch-fails"></a>Сбой запуска службы
После успешной сборки образа система пытается запустить контейнер в ACI или AKS в зависимости от конфигурации развертывания. Обычно рекомендуется сначала использовать для развертывания ACI, так как такое развертывание проще и происходит в одном контейнере. Таким образом затем можно исключить все связанные с AKS проблемы.

В ходе процесса запуска контейнера системой вызывается функция `init()` в скрипте оценки. При наличии неперехваченных исключений в функции `init()` в сообщении об ошибке может появиться ошибка **CrashLoopBackOff**. Ниже приведены некоторые советы по устранению этой проблемы.

### <a name="inspect-the-docker-log"></a>Проверка журнала Docker
Можно распечатать подробные сообщения журнала ядра Docker из объекта службы.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices()['mysvc'].get_logs())
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
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

Он выводит локальный путь (относительно `/var/azureml-app`) в контейнер, где скрипт оценки ожидает найти файл модели или папку с файлами. Затем можно проверить, действительно ли файл или папка находится там, где нужно.


## <a name="function-fails-runinputdata"></a>Ошибка выполнения функции: run(input_data)
Если служба успешно развернута, но аварийно завершает работу при публикации данных в конечную точку оценки, можно добавить оператор для перехвата ошибок в функцию `run(input_data)`, чтобы она возвращала подробное сообщение об ошибке. Например: 

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


## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о развертывании см. в статьях, представленных ниже. 
* [Развертывание моделей с помощью Службы машинного обучения Azure](how-to-deploy-and-where.md)

* [Руководство. Обучение и развертывание моделей](tutorial-train-models-with-aml.md)
