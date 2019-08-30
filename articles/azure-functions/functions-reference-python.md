---
title: Справочник разработчика Python. Функции Azure
description: Сведения о разработке функций на языке Python
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: azure functions, functions, event processing, dynamic compute, serverless architecture, python
ms.service: azure-functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: 16cf6704096f8c1534777ffb1958d2fa858374db
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70170536"
---
# <a name="azure-functions-python-developer-guide"></a>Справочник разработчика Python. Функции Azure

В этой статье содержатся общие сведения о разработке Функций Azure с помощью Python. Предполагается, что вы уже прочли [руководство для разработчиков Функций Azure](functions-reference.md). 

Примеры проектов автономной функции в Python см. в статье [образцы функций Python](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>Модель программирования

Функции Azure предполагают, что функция не имеет состояния, в скрипте Python, который обрабатывает входные данные и выдает выходные данные. По умолчанию среда выполнения ждет, что метод будет реализован как глобальный метод, вызываемый `main()` `__init__.py` в файле. Можно также [указать альтернативную точку входа](#alternate-entry-point).

Данные из триггеров и привязок привязываются к функции через атрибуты метода с `name` помощью свойства, определенного в файле *Function. JSON* . Например, приведенная ниже _функция Function. JSON_ описывает простую функцию, АКТИВИРУЕМУЮ HTTP-запросом с именем `req`:

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

Кроме того, можно явно объявить типы атрибутов и тип возвращаемого значения в функции с помощью аннотаций типа Python. Это помогает использовать функции IntelliSense и автозаполнения, предоставляемые многими редакторами кода Python.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Аннотации Python, включенные в пакет [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python), позволяют привязать входные и выходные данные к методам.

## <a name="alternate-entry-point"></a>Альтернативная точка входа

Можно изменить поведение функции по умолчанию, при необходимости указав `scriptFile` свойства и `entryPoint` в файле *Function. JSON* . Например, приведенный ниже файл _Function. JSON_ сообщает среде выполнения о необходимости `customentry()` использовать метод в файле _Main.py_ в качестве точки входа для функции Azure.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>Структура папок

Структура папок для проекта функций Python выглядит как в следующем примере:

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
```

Существует общий файл [host.json](functions-host-json.md), который можно использовать для настройки приложения-функции. У каждой функции есть собственный файл кода и файл конфигурации привязки. 

Общий код следует располагать в отдельной папке. Для ссылок на модули в папке SharedCode можно использовать следующий синтаксис:

```
from __app__.SharedCode import myFirstHelperFunction
```

При развертывании проекта функции в приложении-функции в Azure весь контент папки *FunctionApp* должен быть добавлен в пакет, но не в саму папку.

## <a name="triggers-and-inputs"></a>Триггеры и входные данные

Входные данные в Функциях Azure делятся на две категории: входные данные триггеров и дополнительные входные данные. Несмотря на то что они различаются `function.json` в файле, использование идентично в коде Python.  Строки подключения или секреты для триггеров и источников входных данных сопоставляются `local.settings.json` со значениями в файле при локальном запуске и параметрами приложения при выполнении в Azure. 

Например, следующий код демонстрирует разницу между двумя:

```json
// function.json
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

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

При активации этой функции HTTP-запрос передается в функцию с помощью `req`. Запись будет извлечена из хранилища больших двоичных объектов Azure на основе _идентификатора_ в URL-адресе маршрута и стала `obj` доступна как в теле функции.  Здесь указанная учетная запись хранения представляет собой строку `AzureWebJobsStorage` подключения, которая является той же учетной записью хранения, используемой приложением-функцией.


## <a name="outputs"></a>Выходные данные

Выходные данные можно выразить как возвращаемое значение или параметры вывода. Если используется только один вывод, мы рекомендуем использовать возвращаемое значение. Для нескольких выводов нужно использовать параметры вывода.

Чтобы использовать возвращаемое значение функции в качестве значения выходной привязки, присвойте свойству `name` значение `$return` в `function.json`.

Чтобы создать несколько выходов, используйте `set()` метод, предоставляемый [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) интерфейсом, чтобы присвоить значение привязке. Например, следующая функция может направлять сообщение в очередь и возвращает ответ HTTP.

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

## <a name="logging"></a>Ведение журнала

Доступ к средству ведения журнала среды выполнения Функций Azure предоставляется через корневой обработчик [`logging`](https://docs.python.org/3/library/logging.html#module-logging) в приложении-функции. Это средство ведения журнала привязано к Application Insights и позволяет отмечать предупреждения и ошибки, возникшие во время выполнения функции.

Следующий пример сохраняет в журнал информационное сообщение, когда функция вызывается с помощью триггера HTTP.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Доступны и другие методы ведения журнала, которые позволяют выводить сообщения в консоль на разных уровнях трассировки.

| Метод                 | Описание                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Записывает сообщение с уровнем CRITICAL в корневое средство ведения журнала.  |
| **`error(_message_)`**   | Записывает сообщение с уровнем ERROR в корневое средство ведения журнала.    |
| **`warning(_message_)`**    | Записывает сообщение с уровнем WARNING в корневое средство ведения журнала.  |
| **`info(_message_)`**    | Записывает сообщение с уровнем INFO в корневое средство ведения журнала.  |
| **`debug(_message_)`** | Записывает сообщение с уровнем DEBUG в корневое средство ведения журнала.  |

Дополнительные сведения о ведении журналов см. в статье [мониторинг функций Azure](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>Триггеры и привязки HTTP

Триггер HTTP определяется в файле Function. Jon. Объект `name` привязки должен соответствовать именованному параметру в функции. В предыдущих примерах используется имя `req` привязки. Этот параметр является объектом [HttpRequest] , и возвращается объект [HttpResponse] .

Из объекта [HttpRequest] можно получить заголовки запроса, параметры запроса, параметры маршрута и текст сообщения. 

Ниже приведен пример из [шаблона триггера HTTP для Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python). 

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')
            
    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

В этой функции значение `name` параметра запроса получается `params` из параметра объекта [HttpRequest] . Текст сообщения в кодировке JSON считывается с помощью `get_json` метода. 

Аналогичным образом можно задать `status_code` и `headers` для ответного сообщения в возвращенном объекте [HttpResponse] .
                                                              
## <a name="async"></a>Асинхронный режим

Мы рекомендуем написать функцию Azure как асинхронную соподпрограмму с помощью `async def` инструкции.

```python
# Will be run with asyncio directly


async def main():
    await some_nonblocking_socket_io_op()
```

Если функция Main () является синхронной (нет квалификатора), мы автоматически выполняем функцию в `asyncio` пуле потоков.

```python
# Would be run in an asyncio thread-pool


def main():
    some_blocking_socket_io()
```

## <a name="context"></a>Контекст

Чтобы получить контекст вызова функции во время выполнения, включите [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) аргумент в сигнатуру. 

Пример:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

Класс [**контекста**](/python/api/azure-functions/azure.functions.context?view=azure-python) имеет следующие методы.

`function_directory`  
Каталог, в котором выполняется функция.

`function_name`  
Имя функции.

`invocation_id`  
Идентификатор текущего вызова функции.

## <a name="global-variables"></a>Глобальные переменные

Не гарантируется, что состояние приложения будет сохранено для будущих выполнений. Однако среда выполнения функций Azure часто использует один и тот же процесс для нескольких выполнений одного и того же приложения. Чтобы кэшировать результаты ресурсоемких вычислений, объявите ее как глобальную переменную. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Переменные среды

В функциях [Параметры приложения](functions-app-settings.md), такие как строки подключения службы, предоставляются как переменные среды во время выполнения. Доступ к этим параметрам можно получить, `import os` объявив и используя `setting = os.environ["setting-name"]`,.

В следующем примере возвращается [параметр приложения](functions-how-to-use-azure-function-app-settings.md#settings)с ключом с именем `myAppSetting`:

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Для локальной разработки параметры приложения хранятся [в файле Local. Settings. JSON](functions-run-local.md#local-settings-file).  

## <a name="python-version-and-package-management"></a>Управление версиями и пакетами Python

Сейчас Функции Azure поддерживают только Python 3.6.x (официальный дистрибутив CPython).

При локальной разработке с помощью Azure Functions Core Tools или Visual Studio Code добавьте имена и версии требуемых пакетов в файл `requirements.txt` и установите их с помощью `pip`.

Например, представленные ниже файл требований и команда pip позволяют установить пакет `requests` из PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Публикация в Azure

Когда все будет готово к публикации, убедитесь, что все зависимости перечислены в файле *требований. txt* , который находится в корне каталога проекта. Функции Azure могут [удаленно создавать](functions-deployment-technologies.md#remote-build) эти зависимости.

Для развертывания в Azure и выполнения удаленной сборки используйте следующую команду:

```bash
func azure functionapp publish <app name> --build remote
```

Если вы не используете удаленную сборку и используете пакет, который требует компилятора и не поддерживает установку многих колес, совместимых с Linux, из PyPI, публикация в Azure без локального построения завершится со следующей ошибкой:

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

Чтобы выполнить сборку локально и настроить необходимые двоичные файлы, [установите DOCKER](https://docs.docker.com/install/) на локальном компьютере и выполните следующую команду для публикации с помощью [Azure functions Core Tools](functions-run-local.md#v2) (Func). Не забудьте заменить `<app name>` именем приложения-функции, размещенного в Azure. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

На системном уровне средства Core Tools используют Docker для запуска образа [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) в виде контейнера на локальном компьютере. С помощью этой среды он затем создает и устанавливает необходимые модули из исходного распространения, прежде чем упаковывать их для окончательного развертывания в Azure.

Чтобы собрать зависимости и опубликовать их с помощью системы непрерывной поставки (CD), [используйте Azure pipelines](functions-how-to-azure-devops.md). 

## <a name="unit-testing"></a>Модульное тестирование

Функции, написанные на языке Python, можно тестировать так же, как и другой код Python, используя стандартные платформы тестирования. Для большинства привязок можно создать макетный объект ввода, создав экземпляр соответствующего класса из `azure.functions` пакета. Так как `requirements.txt` пакет не доступен сразу же, обязательно установите его с помощью файла, как описано в разделе о [версии и управлении пакетами Python](#python-version-and-package-management) выше. [`azure.functions`](https://pypi.org/project/azure-functions/)

Например, ниже приведено фиктивное тестирование функции, активируемой HTTP:

```json
{
  "scriptFile": "httpfunc.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

```python
# myapp/httpfunc.py
import azure.functions as func
import logging

def my_function(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

```python
# myapp/test_httpfunc.py
import unittest

import azure.functions as func
from httpfunc import my_function


class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/HttpTrigger',
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'Hello Test',
        )
```

Вот еще один пример с функцией, активируемой в очереди:

```python
# myapp/__init__.py
import azure.functions as func


def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# myapp/test_func.py
import unittest

import azure.functions as func
from . import my_function


class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock Queue message.
        req = func.QueueMessage(
            body=b'test')

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp,
            'msg body: test',
        )
```

## <a name="known-issues-and-faq"></a>Известные проблемы и часто задаваемые вопросы

Все известные проблемы и запросы возможностей отслеживаются в [списке проблем на GitHub](https://github.com/Azure/azure-functions-python-worker/issues). Если вы столкнулись с проблемой и не можете найти ее решение на GitHub, откройте новую проблему и укажите ее подробное описание.

### <a name="cross-origin-resource-sharing"></a>Предоставление общего доступа к ресурсам независимо от источника

Функции Azure поддерживают общий доступ к ресурсам между источниками (CORS). CORS настраивается [на портале](functions-how-to-use-azure-function-app-settings.md#cors) и с помощью [Azure CLI](/cli/azure/functionapp/cors). Список разрешенных источников CORS применяется на уровне приложения функции. При включении CORS ответы включают `Access-Control-Allow-Origin` заголовок. Дополнительные сведения см. в разделе [совместное использование ресурсов между источниками](functions-how-to-use-azure-function-app-settings.md#cors).

Список разрешенных источников [сейчас не поддерживается](https://github.com/Azure/azure-functions-python-worker/issues/444) для приложений функций Python. Из-за этого ограничения необходимо явно задать `Access-Control-Allow-Origin` заголовок в функциях HTTP, как показано в следующем примере:

```python
def main(req: func.HttpRequest) -> func.HttpResponse:

    # Define the allow origin headers.
    headers = {"Access-Control-Allow-Origin": "https://contoso.com"}

    # Set the headers in the response.
    return func.HttpResponse(
            f"Allowed origin '{headers}'.",
            headers=headers, status_code=200
    )
``` 

Убедитесь, что вы также обновляете функцию Function. JSON для поддержки метода HTTP OPTIONS:

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

Этот метод используется браузером Chrome для согласования списка разрешенных источников. 

## <a name="next-steps"></a>Следующие шаги

Для получения дополнительных сведений см. следующие ресурсы:

* [Документация по API пакета функций Azure](/python/api/azure-functions/azure.functions?view=azure-python)
* [Рекомендации по функциям Azure](functions-best-practices.md)
* [Azure Functions triggers and bindings (Триггеры и привязки в Функциях Azure)](functions-triggers-bindings.md)
* [Привязки хранилища BLOB-объектов Azure для службы "Функции Azure"](functions-bindings-storage-blob.md)
* [Триггеры и привязки HTTP в службе "Функции Azure"](functions-bindings-http-webhook.md)
* [Привязки хранилища очередей Azure для службы "Функции Azure"](functions-bindings-storage-queue.md)
* [Триггер таймера](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python