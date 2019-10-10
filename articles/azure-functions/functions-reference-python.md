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
ms.openlocfilehash: 4fd73f528ac823a8e794a880f87dd5f8872e1251
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243276"
---
# <a name="azure-functions-python-developer-guide"></a>Справочник разработчика Python. Функции Azure

В этой статье содержатся общие сведения о разработке Функций Azure с помощью Python. Предполагается, что вы уже прочли [руководство для разработчиков Функций Azure](functions-reference.md). 

Примеры проектов автономной функции в Python см. в статье [образцы функций Python](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>Модель программирования

Функции Azure предполагают, что функция не имеет состояния, в скрипте Python, который обрабатывает входные данные и выдает выходные данные. По умолчанию среда выполнения ждет, что метод будет реализован как глобальный метод с именем `main()` в файле `__init__.py`. Можно также [указать альтернативную точку входа](#alternate-entry-point).

Данные из триггеров и привязок привязываются к функции через атрибуты метода с помощью свойства `name`, определенного в файле *Function. JSON* . Например, приведенная ниже _функция Function. JSON_ описывает простую функцию, активируемую запросом HTTP с именем `req`:

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

Можно изменить поведение функции по умолчанию, при необходимости указав свойства `scriptFile` и `entryPoint` в файле *Function. JSON* . Например, приведенный ниже файл _Function. JSON_ сообщает среде выполнения о необходимости использовать метод `customentry()` в файле _Main.py_ в качестве точки входа для функции Azure.

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
 | | - example.py
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

Для ссылки на модули, локальные для функции, можно использовать синтаксис относительного импорта следующим образом:

```
from . import example
```

При развертывании проекта функции в приложении-функции в Azure весь контент папки *FunctionApp* должен быть добавлен в пакет, но не в саму папку.

## <a name="triggers-and-inputs"></a>Триггеры и входные данные

Входные данные в Функциях Azure делятся на две категории: входные данные триггеров и дополнительные входные данные. Несмотря на то что они отличаются в файле `function.json`, использование идентично в коде Python.  Строки подключения или секреты для триггеров и источников входных данных сопоставляются со значениями в файле `local.settings.json` при выполнении в локальной среде, а параметры приложения — при запуске в Azure. 

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

При активации этой функции HTTP-запрос передается в функцию с помощью `req`. Запись будет извлечена из хранилища больших двоичных объектов Azure на основе _идентификатора_ в URL-адресе маршрута и стала доступна как `obj` в теле функции.  Здесь указанная учетная запись хранения является строкой подключения в, которая является той же учетной записью хранения, используемой приложением-функцией.


## <a name="outputs"></a>outputs

Выходные данные можно выразить как возвращаемое значение или параметры вывода. Если используется только один вывод, мы рекомендуем использовать возвращаемое значение. Для нескольких выводов нужно использовать параметры вывода.

Чтобы использовать возвращаемое значение функции в качестве значения выходной привязки, присвойте свойству `name` значение `$return` в `function.json`.

Чтобы создать несколько выходов, используйте метод `set()`, предоставляемый интерфейсом [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) , чтобы присвоить значение привязке. Например, следующая функция может направлять сообщение в очередь и возвращает ответ HTTP.

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

Триггер HTTP определяется в файле Function. Jon. @No__t-0 привязки должен соответствовать именованному параметру в функции. В предыдущих примерах используется имя привязки `req`. Этот параметр является объектом [HttpRequest] , и возвращается объект [HttpResponse] .

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

В этой функции значение параметра запроса `name` берется из параметра `params` объекта [HttpRequest] . Текст сообщения в кодировке JSON считывается с помощью метода `get_json`. 

Аналогичным образом можно задать `status_code` и `headers` для ответного сообщения в возвращенном объекте [HttpResponse] .

## <a name="concurrency"></a>Параллелизм

По умолчанию среда выполнения Python для функций может обрабатывать только один вызов функции за раз. Этот уровень параллелизма может быть недостаточным при одном или нескольких следующих условиях:

+ Выполняется попытка одновременного выполнения нескольких вызовов.
+ Выполняется обработка большого количества событий ввода-вывода.
+ Ваше приложение привязано к вводу-выводу.

В таких ситуациях можно повысить производительность, запустив асинхронно и используя несколько языковых рабочих процессов.  

### <a name="async"></a>Асинхронный режим

Рекомендуется использовать оператор `async def`, чтобы функция выполнялась как асинхронная соподпрограмма.

```python
# Runs with asyncio directly

async def main():
    await some_nonblocking_socket_io_op()
```

Если функция `main()` является синхронной (без квалификатора `async`), функция автоматически выполняется в пуле потоков `asyncio`.

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Использование нескольких языковых рабочих процессов

По умолчанию каждый экземпляр узла функций имеет один рабочий процесс с одним языком. Однако поддержка нескольких рабочих процессов языка на экземпляре узла не поддерживается. Вызовы функций могут быть равномерно распределены между этими рабочими процессами языка. Чтобы изменить это значение, используйте параметр приложения [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) . 

## <a name="context"></a>Контекст

Чтобы получить контекст вызова функции во время выполнения, включите в сигнатуру аргумент [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) . 

Пример:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

Класс [**контекста**](/python/api/azure-functions/azure.functions.context?view=azure-python) имеет следующие строковые атрибуты:

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

В функциях [Параметры приложения](functions-app-settings.md), такие как строки подключения службы, предоставляются как переменные среды во время выполнения. Вы можете получить доступ к этим параметрам, объявляя `import os`, а затем используя, `setting = os.environ["setting-name"]`.

В следующем примере показано получение [параметра приложения](functions-how-to-use-azure-function-app-settings.md#settings)с ключом с именем `myAppSetting`:

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

Файлы проекта и папки, исключаемые из публикации, включая папку виртуальной среды, перечислены в файле. фунЦигноре.  

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

Функции, написанные на языке Python, можно тестировать так же, как и другой код Python, используя стандартные платформы тестирования. Для большинства привязок можно создать макетный объект ввода, создав экземпляр соответствующего класса из пакета `azure.functions`. Так как пакет [`azure.functions`](https://pypi.org/project/azure-functions/) недоступен сразу же, обязательно установите его с помощью файла `requirements.txt`, как описано в разделе о [версиях и управлении пакетами Python](#python-version-and-package-management) выше.

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

Функции Azure поддерживают общий доступ к ресурсам между источниками (CORS). CORS настраивается [на портале](functions-how-to-use-azure-function-app-settings.md#cors) и с помощью [Azure CLI](/cli/azure/functionapp/cors). Список разрешенных источников CORS применяется на уровне приложения функции. При включении CORS ответы включают заголовок `Access-Control-Allow-Origin`. Дополнительные сведения см. в разделе [совместное использование ресурсов между источниками](functions-how-to-use-azure-function-app-settings.md#cors).

Список разрешенных источников [сейчас не поддерживается](https://github.com/Azure/azure-functions-python-worker/issues/444) для приложений функций Python. Из-за этого ограничения необходимо явно задать заголовок `Access-Control-Allow-Origin` в функциях HTTP, как показано в следующем примере:

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
