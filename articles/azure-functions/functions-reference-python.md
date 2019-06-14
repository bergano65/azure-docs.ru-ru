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
ms.openlocfilehash: d25082c429c58c074726c75f7ff6f248daee4151
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050617"
---
# <a name="azure-functions-python-developer-guide"></a>Справочник разработчика Python. Функции Azure

В этой статье содержатся общие сведения о разработке Функций Azure с помощью Python. Предполагается, что вы уже прочли [руководство для разработчиков Функций Azure](functions-reference.md).

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

## <a name="programming-model"></a>Модель программирования

Функция Azure должна быть реализована как метод без отслеживания состояния в скрипте Python, который обрабатывает входные данные и создает выходные данные. По умолчанию среда выполнения ожидает, что метод реализуется как глобальный метод с именем `main()` в `__init__.py` файл.

Конфигурация по умолчанию можно изменить, указав `scriptFile` и `entryPoint` свойств в *function.json* файла. Например _function.json_ ниже сообщает среде выполнения использовать `customentry()` метод в _main.py_ файл в качестве точки входа для функции Azure.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

Данные из триггеров и привязок будут привязаны к функции с помощью атрибутов метода, с помощью `name` свойство, определенное в *function.json* файла. Например _function.json_ ниже описывает простой функции, активируемой HTTP-запроса с именем `req`:

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

При необходимости использовать intellisense и автозавершение, обеспечиваемые редактора кода, можно также объявить типы атрибутов и тип возвращаемого значения в функцию с помощью аннотации типов Python. 

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
```

Существует общий файл [host.json](functions-host-json.md), который можно использовать для настройки приложения-функции. У каждой функции есть собственный файл кода и файл конфигурации привязки. 

Общий код следует располагать в отдельной папке. Для ссылок на модули в папке SharedCode можно использовать следующий синтаксис:

```
from __app__.SharedCode import myFirstHelperFunction
```

При развертывании проекта функции в приложении-функции в Azure, все содержимое *FunctionApp* папки должны быть включены в пакет, но не в самой папке.

## <a name="triggers-and-inputs"></a>Триггеры и входные данные

Входные данные в Функциях Azure делятся на две категории: входные данные триггеров и дополнительные входные данные. Несмотря на то, что они отличаются в `function.json` , использования, идентичный в коде Python.  Строки подключения или секреты для триггера и входные источники сопоставить значения в `local.settings.json` файл при локальном запуске и параметры приложения при работе в Azure. 

Например следующий код демонстрирует различие между ними:

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

При активации этой функции HTTP-запрос передается в функцию с помощью `req`. Запись извлекается из хранилища BLOB-объектов Azure, на основе _идентификатор_ в URL-адрес маршрута и сделать доступной в качестве `obj` в теле функции.  Здесь учетной записи хранения указано, находится в строке подключения `AzureWebJobsStorage` это же учетной записи хранения, используемые приложения-функции.


## <a name="outputs"></a>outputs

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

| Метод                 | Описание                                |
| ---------------------- | ------------------------------------------ |
| logging.**critical(_сообщение_)**   | Записывает сообщение с уровнем CRITICAL в корневое средство ведения журнала.  |
| logging.**error(_сообщение_)**   | Записывает сообщение с уровнем ERROR в корневое средство ведения журнала.    |
| logging.**warning(_сообщение_)**    | Записывает сообщение с уровнем WARNING в корневое средство ведения журнала.  |
| logging.**info(_сообщение_)**    | Записывает сообщение с уровнем INFO в корневое средство ведения журнала.  |
| logging.**debug(_сообщение_)** | Записывает сообщение с уровнем DEBUG в корневое средство ведения журнала.  |

## <a name="async"></a>Асинхронный режим

Мы рекомендуем создавать функции Azure в качестве асинхронной соподпрограмме с помощью `async def` инструкции.

```python
# Will be run with asyncio directly
async def main():
    await some_nonblocking_socket_io_op()
```

Если функции main() является синхронным (не `async` квалификатор) мы автоматически запустите ее в `asyncio` пул потоков.

```python
# Would be run in an asyncio thread-pool
def main():
    some_blocking_socket_io()
```

## <a name="context"></a>Context

Чтобы получить контекст вызова функции во время выполнения, включите в его подпись аргумент `context`. 

Пример:

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

## <a name="global-variables"></a>Глобальные переменные

Не гарантируется, что состояние приложения будет сохранен для последующего использования. Тем не менее среда выполнения функций Azure часто повторно использует один процесс для нескольких выполнений одного приложения. Чтобы кэшировать результаты ресурсоемких вычислений, объявите ее в качестве глобальной переменной. 

```python
CACHED_DATA = None

def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

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

Когда вы будете готовы к публикации, убедитесь, что все зависимости перечислены в *requirements.txt* файл, расположенный в корневом каталоге проекта. Если вы используете пакет, для которого требуется компилятор и не поддерживается установка wheel из PyPI, совместимых с manylinux, публикация в Azure завершится ошибкой со следующим сообщением: 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

Чтобы автоматически компилировать и настраивать требуемые двоичные файлы, [установите Docker](https://docs.docker.com/install/) на локальном компьютере и выполните следующую команду для публикации с помощью [Azure Functions Core Tools](functions-run-local.md#v2) (функция). Не забудьте заменить `<app name>` именем приложения-функции, размещенного в Azure. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

На системном уровне средства Core Tools используют Docker для запуска образа [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) в виде контейнера на локальном компьютере. Затем на основе этой среды они компилируют и устанавливают необходимые модули из исходного дистрибутива, а затем упаковывают их для окончательного развертывания в Azure.

Для создания зависимостей и публикации с помощью системы непрерывной поставки (CD), [используйте конвейеры DevOps Azure](https://docs.microsoft.com/azure/azure-functions/functions-how-to-azure-devops). 

## <a name="unit-testing"></a>Модульное тестирование

Функции, написанные на Python можно тестировать как другой код Python, с помощью стандартной платформы тестирования. Для большинства привязок, можно создать макет входного объекта создается экземпляр соответствующего класса со `azure.functions` пакета.

Например ниже приведен макетов, чтобы проверить функции, активируемой HTTP.

```python
# myapp/__init__.py
import azure.functions as func
import logging

def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

```python
# myapp/test_func.py
import unittest

import azure.functions as func
from . import my_function

class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/my_function', 
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(), 
            'Hello, Test!',
        )
```

Вот еще один пример, с помощью функции, активируемой очередью:

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

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительных сведений см. следующие ресурсы:

* [Рекомендации по функциям Azure](functions-best-practices.md)
* [Azure Functions triggers and bindings (Триггеры и привязки в Функциях Azure)](functions-triggers-bindings.md)
* [Привязки хранилища BLOB-объектов Azure для службы "Функции Azure"](functions-bindings-storage-blob.md)
* [Триггеры и привязки HTTP в службе "Функции Azure"](functions-bindings-http-webhook.md)
* [Привязки хранилища очередей Azure для службы "Функции Azure"](functions-bindings-storage-queue.md)
* [Триггер таймера](functions-bindings-timer.md)
