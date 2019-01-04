---
title: Справочник разработчика Python. Функции Azure
description: Сведения о разработке функций на языке Python.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: azure functions, functions, event processing, dynamic compute, serverless architecture, python
ms.service: functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: 619db07204b88609314d0d3d06709eaa93cb7a43
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53188040"
---
# <a name="azure-functions-python-developer-guide"></a>Справочник разработчика Python. Функции Azure

В этой статье содержатся общие сведения о разработке Функций Azure с помощью Python. Предполагается, что вы уже прочли [руководство для разработчиков Функций Azure](functions-reference.md).

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

## <a name="programming-model"></a>Модель программирования

Функция Azure должна быть реализована как метод без отслеживания состояния в скрипте Python, который обрабатывает входные данные и создает выходные данные. По умолчанию среда выполнения ожидает, что функция реализована как глобальный метод с именем `main()` в файле `__init__.py`.

Конфигурацию по умолчанию можно изменить, указав свойств `scriptFile` и `entryPoint` в файле `function.json`. Пример файла _function.json_ ниже указывает, что среда выполнения должна использовать метод _customentry()_ из файла _main.py_ в качестве точки входа для функции Azure.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

Данные из триггеров и привязок будут привязаны к функции через атрибуты метода с помощью свойства `name`, определяемого в файле конфигурации `function.json`. Пример файла _function.json_ ниже описывает простую функцию с именем `req`, которая активируется HTTP-запросом.

```json
{
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

Файл `__init__.py` содержит следующий код функции:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Если потребуется, вы можете объявить для функции типы параметров и тип возвращаемого значения с помощью аннотации типов Python. Например, с использованием аннотаций та же функция будет выглядеть следующим образом:

```python
import azure.functions

def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```  

Аннотации Python, включенные в пакет [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python), позволяют привязать входные и выходные данные к методам. 

## <a name="folder-structure"></a>Структура папок

Структура папок для проекта Функций на Python выглядит следующим образом:

```
 FunctionApp
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
 | - extensions.csproj
 | - bin
```

Существует общий файл [host.json](functions-host-json.md), который можно использовать для настройки приложения-функции. У каждой функции есть собственный файл кода и файл конфигурации привязки. 

Общий код следует располагать в отдельной папке. Для ссылок на модули в папке SharedCode можно использовать следующий синтаксис:

```
from ..SharedCode import myFirstHelperFunction
```

Расширения привязки, используемые средой выполнения функций, определяются в файле `extensions.csproj`, а фактические файлы библиотеки размещаются в папке `bin`. При локальной разработке необходимо [зарегистрировать расширения привязки](functions-triggers-bindings.md#local-development-azure-functions-core-tools) с помощью Azure Functions Core Tools. 

При развертывании проекта Функций в приложении-функции Azure следует включить в пакет все содержимое папки FunctionApp, но не саму эту папку.

## <a name="inputs"></a>Входные данные

Входные данные в Функциях Azure делятся на две категории: входные данные триггеров и дополнительные входные данные. Они по-разному представлены в `function.json`, но использование в коде Python полностью идентично. Давайте рассмотрим в качестве примера следующий фрагмент кода:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```python
import azure.functions as func
import logging

def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

При активации этой функции HTTP-запрос передается в функцию с помощью `req`. Запись извлекается из хранилища BLOB-объектов Azure по значению _идентификатора_, включенного в URL-адрес маршрута, и становится доступной в виде `obj` в тексте функции.

## <a name="outputs"></a>Выходные данные

Выходные данные можно выразить как возвращаемое значение или параметры вывода. Если используется только один вывод, мы рекомендуем использовать возвращаемое значение. Для нескольких выводов нужно использовать параметры вывода.

Чтобы использовать возвращаемое значение функции в качестве значения выходной привязки, присвойте свойству `name` значение `$return` в `function.json`.

Чтобы создать несколько выходных значений, используйте метод `set()` из интерфейса `azure.functions.Out`, чтобы присвоить значение привязке. Например, следующая функция может направлять сообщение в очередь и возвращает ответ HTTP.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func

def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>Ведение журналов

Доступ к средству ведения журнала среды выполнения Функций Azure предоставляется через корневой обработчик [`logging`](https://docs.python.org/3/library/logging.html#module-logging) в приложении-функции. Это средство ведения журнала привязано к Application Insights и позволяет отмечать предупреждения и ошибки, возникшие во время выполнения функции.

Следующий пример сохраняет в журнал информационное сообщение, когда функция вызывается с помощью триггера HTTP.

```python
import logging

def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Доступны и другие методы ведения журнала, которые позволяют выводить сообщения в консоль на разных уровнях трассировки.

| Метод                 | ОПИСАНИЕ                                |
| ---------------------- | ------------------------------------------ |
| logging.**critical(_сообщение_)**   | Записывает сообщение с уровнем CRITICAL в корневое средство ведения журнала.  |
| logging.**error(_сообщение_)**   | Записывает сообщение с уровнем ERROR в корневое средство ведения журнала.    |
| logging.**warning(_сообщение_)**    | Записывает сообщение с уровнем WARNING в корневое средство ведения журнала.  |
| logging.**info(_сообщение_)**    | Записывает сообщение с уровнем INFO в корневое средство ведения журнала.  |
| logging.**debug(_сообщение_)** | Записывает сообщение с уровнем DEBUG в корневое средство ведения журнала.  |

## <a name="importing-shared-code-into-a-function-module"></a>Импорт общего кода в модуль функции

Модули Python, которые публикуются вместе с модулями функции, следует импортировать с использованием синтаксиса относительного импорта:

```python
from . import helpers  # Use more dots to navigate up the folder structure.
def main(req: func.HttpRequest):
    helpers.process_http_request(req)
```

Также вы можете поместить общий код в автономный пакет, опубликовать его в общедоступный или частный экземпляр PyPI и указать как обычную зависимость.

## <a name="async"></a>Асинхронный режим

Так как для приложения-функции может существовать только один процесс Python, мы рекомендуем реализовать Функцию Azure в виде асинхронной подпрограммы с помощью инструкции `async def`.

```python
# Will be run with asyncio directly
async def main():
    await some_nonblocking_socket_io_op()
```

Если функция main() является синхронной (не имеет квалификатора `async`), мы автоматически запускаем ее в пуле потоков `asyncio`.

```python
# Would be run in an asyncio thread-pool
def main():
    some_blocking_socket_io()
```

## <a name="context"></a>Context

Чтобы получить контекст вызова функции во время выполнения, включите в его подпись аргумент `context`. 

Например: 

```python
import azure.functions

def main(req: azure.functions.HttpRequest,
            context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

Класс **Context** содержит следующие методы:

`function_directory`  
Каталог, в котором выполняется функция.

`function_name`  
Имя функции.

`invocation_id`  
Идентификатор текущего вызова функции.

## <a name="python-version-and-package-management"></a>Управление версиями и пакетами Python

Сейчас Функции Azure поддерживают только Python 3.6.x (официальный дистрибутив CPython).

При локальной разработке с помощью Azure Functions Core Tools или Visual Studio Code добавьте имена и версии требуемых пакетов в файл `requirements.txt` и установите их с помощью `pip`.

Например, представленные ниже файл требований и команда pip позволяют установить пакет `requests` из PyPI.

```bash
pip install requests
```

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

Когда вы будете готовы к публикации, убедитесь, что все зависимости перечислены в файле `requirements.txt`, который расположен в корневом каталоге проекта. Чтобы успешно выполнять Функции Azure, файл требований должен содержать по меньшей мере следующие пакеты:

```txt
azure-functions
azure-functions-worker
grpcio==1.14.1
grpcio-tools==1.14.1
protobuf==3.6.1
six==1.11.0
```

## <a name="publishing-to-azure"></a>Публикация в Azure

Если вы используете пакет, для которого требуется компилятор и не поддерживается установка wheel из PyPI, совместимых с manylinux, публикация в Azure завершится ошибкой со следующим сообщением: 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

Чтобы автоматически компилировать и настраивать требуемые двоичные файлы, [установите Docker](https://docs.docker.com/install/) на локальном компьютере и выполните следующую команду для публикации с помощью [Azure Functions Core Tools](functions-run-local.md#v2) (функция). Не забудьте заменить `<app name>` именем приложения-функции, размещенного в Azure. 

```bash
func azure functionapp <app name> --build-native-deps
```

На системном уровне средства Core Tools используют Docker для запуска образа [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) в виде контейнера на локальном компьютере. Затем на основе этой среды они компилируют и устанавливают необходимые модули из исходного дистрибутива, а затем упаковывают их для окончательного развертывания в Azure.

> [!NOTE]
> Решение Core Tools (функция) выполняет программу PyInstaller для переноса фиксированного снимка пользовательского кода и всех зависимостей в единый автономный исполняемый файл для запуска в Azure. Эта функция доступна в предварительной версии и может не работать для всех типов пакетов Python. Если вам не удается импортировать модули, попробуйте повторить публикацию с параметром `--no-bundler`. 
> ```
> func azure functionapp publish <app_name> --build-native-deps --no-bundler
> ```
> Если проблемы сохранятся, сообщите нам об этом через [соответствующий интерфейс ](https://github.com/Azure/azure-functions-core-tools/issues/new) и подробно опишите ситуацию. 


Чтобы выполнять компиляцию и публикацию зависимостей через систему непрерывной интеграции (CI) и непрерывной поставки (CD), вы можете применить [конвейер Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=vsts) или [пользовательский скрипт Travis CI](https://docs.travis-ci.com/user/deployment/script/). 

Ниже приведен пример скрипта `azure-pipelines.yml` для процесса сборки и публикации.
```yml
pool:
  vmImage: 'Ubuntu 16.04'

steps:
- task: NodeTool@0
  inputs:
    versionSpec: '8.x'

- script: |
    set -e
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
    curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
    sudo apt-get install -y apt-transport-https
    echo "install Azure CLI..."
    sudo apt-get update && sudo apt-get install -y azure-cli
    npm i -g azure-functions-core-tools --unsafe-perm true
    echo "installing dotnet core"
    curl -sSL https://dot.net/v1/dotnet-install.sh | bash /dev/stdin --channel 2.0
- script: |
    set -e
    az login --service-principal --username "$(APP_ID)" --password "$(PASSWORD)" --tenant "$(TENANT_ID)" 
    func settings add FUNCTIONS_WORKER_RUNTIME python
    func extensions install
    func azure functionapp publish $(APP_NAME) --build-native-deps
```

Ниже приведен пример скрипта `.travis.yaml` для процесса сборки и публикации.

```yml
sudo: required

language: node_js

node_js:
  - "8"

services:
  - docker

before_install:
  - echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
  - curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
  - sudo apt-get install -y apt-transport-https
  - sudo apt-get update && sudo apt-get install -y azure-cli
  - npm i -g azure-functions-core-tools --unsafe-perm true


script:
  - az login --service-principal --username "$APP_ID" --password "$PASSWORD" --tenant "$TENANT_ID"
  - az account get-access-token --query "accessToken" | func azure functionapp publish $APP_NAME --build-native-deps

```

## <a name="known-issues-and-faq"></a>Известные проблемы и часто задаваемые вопросы

Все известные проблемы и запросы возможностей отслеживаются в [списке проблем на GitHub](https://github.com/Azure/azure-functions-python-worker/issues). Если вы столкнулись с проблемой и не можете найти ее решение на GitHub, откройте новую проблему и укажите ее подробное описание.

## <a name="next-steps"></a>Дополнительная информация

Для получения дополнительных сведений см. следующие ресурсы:

* [Рекомендации по функциям Azure](functions-best-practices.md)
* [Azure Functions triggers and bindings (Триггеры и привязки в Функциях Azure)](functions-triggers-bindings.md)
* [Привязки хранилища BLOB-объектов Azure для службы "Функции Azure"](functions-bindings-storage-blob.md)
* [Триггеры и привязки HTTP в службе "Функции Azure"](functions-bindings-http-webhook.md)
* [Привязки хранилища очередей Azure для службы "Функции Azure"](functions-bindings-storage-queue.md)
* [Триггер таймера](functions-bindings-timer.md)
