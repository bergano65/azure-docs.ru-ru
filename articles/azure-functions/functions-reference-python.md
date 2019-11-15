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
ms.openlocfilehash: 01d8560ee2752f21eb52c00f4c337d1dca59b8fb
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082696"
---
# <a name="azure-functions-python-developer-guide"></a>Справочник разработчика Python. Функции Azure

В этой статье содержатся общие сведения о разработке Функций Azure с помощью Python. Предполагается, что вы уже прочли [руководство для разработчиков Функций Azure](functions-reference.md). 

Примеры проектов автономной функции в Python см. в статье [образцы функций Python](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>Модель программирования

Функции Azure предполагают, что функция не имеет состояния, в скрипте Python, который обрабатывает входные данные и выдает выходные данные. По умолчанию среда выполнения ждет, что метод будет реализован как глобальный метод, именуемый `main()` в `__init__.py`ном файле. Можно также [указать альтернативную точку входа](#alternate-entry-point).

Данные из триггеров и привязок привязываются к функции через атрибуты метода с помощью свойства `name`, определенного в файле *Function. JSON* . Например, приведенная ниже _функция Function. JSON_ описывает простую функцию, АКТИВИРУЕМУЮ HTTP-запросом с именем `req`:

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

Рекомендуемая структура папок для проекта функций Python выглядит как в следующем примере:

```
 __app__
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
 tests
```
Главная папка проекта (\_\_приложение\_\_) может содержать следующие файлы:

* *Local. Settings. JSON*: используется для хранения параметров приложения и строк подключения при локальном запуске. Этот файл не публикуется в Azure. Дополнительные сведения см. в разделе [Local. Settings. File](functions-run-local.md#local-settings-file).
* *требования. txt*: содержит список пакетов, устанавливаемых системой при публикации в Azure.
* *Host. JSON*: содержит глобальные параметры конфигурации, влияющие на все функции в приложении-функции. Этот файл не публикуется в Azure. При локальном запуске поддерживаются не все параметры. Дополнительные сведения см. в разделе [Host. JSON](functions-host-json.md).
* *фунЦигноре*(необязательный). объявляет файлы, которые не должны публиковаться в Azure.
* *gitignore*(необязательный). объявляет файлы, исключаемые из репозитория Git, например Local. Settings. JSON.

У каждой функции есть собственный файл кода и файл конфигурации привязки. 

Общий код должен храниться в отдельной папке в \_\_приложении\_\_. Для ссылок на модули в папке SharedCode можно использовать следующий синтаксис:

```python
from __app__.SharedCode import myFirstHelperFunction
```

Для ссылки на модули, локальные для функции, можно использовать синтаксис относительного импорта следующим образом:

```python
from . import example
```

При развертывании проекта в приложении-функции в Azure весь контент папки *FunctionApp* должен быть добавлен в пакет, но не в саму папку. Рекомендуется хранить тесты в папке, отдельной от папки проекта, в этом примере `tests`. Это позволяет не развертывать тестовый код в приложении. Дополнительные сведения см. в разделе [модульное тестирование](#unit-testing).

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


## <a name="outputs"></a>Выходные данные

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
| **`critical(_message_)`**   | Записывает сообщение с уровнем CRITICAL в корневое средство ведения журнала.  |
| **`error(_message_)`**   | Записывает сообщение с уровнем ERROR в корневое средство ведения журнала.    |
| **`warning(_message_)`**    | Записывает сообщение с уровнем WARNING в корневое средство ведения журнала.  |
| **`info(_message_)`**    | Записывает сообщение с уровнем INFO в корневое средство ведения журнала.  |
| **`debug(_message_)`** | Записывает сообщение с уровнем DEBUG в корневое средство ведения журнала.  |

Дополнительные сведения о ведении журналов см. в статье [мониторинг функций Azure](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>Триггеры и привязки HTTP

Триггер HTTP определяется в файле Function. Jon. `name` привязки должен соответствовать именованному параметру в функции. В предыдущих примерах используется имя привязки `req`. Этот параметр является объектом [HttpRequest] , и возвращается объект [HttpResponse] .

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

В этой функции значение параметра запроса `name` получено из параметра `params` объекта [HttpRequest] . Текст сообщения в кодировке JSON считывается с помощью метода `get_json`. 

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

Если функция `main()` является синхронной (без квалификатора `async`), функция автоматически выполняется в `asyncio`ном пуле потоков.

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Использование нескольких языковых рабочих процессов

По умолчанию каждый экземпляр узла функций имеет один рабочий процесс с одним языком. Однако поддержка нескольких рабочих процессов языка на экземпляре узла не поддерживается. Вызовы функций могут быть равномерно распределены между этими рабочими процессами языка. Чтобы изменить это значение, используйте параметр приложения [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) . 

## <a name="context"></a>Контекст

Чтобы получить контекст вызова функции во время выполнения, включите в сигнатуру аргумент [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) . 

Например,

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

В функциях [Параметры приложения](functions-app-settings.md), такие как строки подключения службы, предоставляются как переменные среды во время выполнения. Доступ к этим параметрам можно получить, объявляя `import os` а затем используя `setting = os.environ["setting-name"]`.

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

## <a name="python-version"></a>Версия Python 

В настоящее время функции Azure поддерживают как Python 3.6. x, так и 3.7. x (официальное распространение CPython). При локальном запуске среда выполнения использует доступную версию Python. Чтобы запросить конкретную версию Python при создании приложения-функции в Azure, используйте параметр `--runtime-version` команды [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) .  

## <a name="package-management"></a>Управление пакетами

При локальной разработке с помощью Azure Functions Core Tools или Visual Studio Code добавьте имена и версии требуемых пакетов в файл `requirements.txt` и установите их с помощью `pip`. 

Например, представленные ниже файл требований и команда pip позволяют установить пакет `requests` из PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Публикация в Azure

Когда все будет готово к публикации, убедитесь, что все общедоступные зависимости перечислены в файле требований. txt, который находится в корне каталога проекта. 

Файлы проекта и папки, исключаемые из публикации, включая папку виртуальной среды, перечислены в файле. фунЦигноре.

Для публикации проекта Python в Azure поддерживаются три действия сборки:

+ Удаленная сборка: зависимости получаются удаленно на основе содержимого файла требований. txt. В качестве рекомендуемого метода сборки рекомендуется использовать [удаленную сборку](functions-deployment-technologies.md#remote-build) . Remote также является вариантом сборки средств Azure по умолчанию. 
+ Локальная сборка. зависимости получаются локально на основе содержимого файла требований. txt. 
+ Пользовательские зависимости. в проекте используются пакеты, не являющиеся общедоступными для наших средств. (Требуется DOCKER.)

Чтобы собрать зависимости и опубликовать их с помощью системы непрерывной поставки (CD), [используйте Azure pipelines](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Удаленная сборка

По умолчанию Azure Functions Core Tools запрашивает удаленную сборку, если вы используете следующую команду [Func Azure functionapp Publish](functions-run-local.md#publish) для публикации проекта Python в Azure. 

```bash
func azure functionapp publish <APP_NAME>
```

Не забудьте заменить `<APP_NAME>` именем приложения-функции, размещенного в Azure.

[Расширение "функции Azure" для Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) также запрашивает удаленную сборку по умолчанию. 

### <a name="local-build"></a>Локальная сборка

Вы можете запретить удаленную сборку, используя следующую команду [Func Azure functionapp Publish](functions-run-local.md#publish) для публикации с локальной сборкой. 

```command
func azure functionapp publish <APP_NAME> --build local
```

Не забудьте заменить `<APP_NAME>` именем приложения-функции, размещенного в Azure. 

С помощью параметра `--build local` зависимости проекта считываются из файла требований. txt, и эти зависимые пакеты загружаются и устанавливаются локально. Файлы проекта и зависимости развертываются с локального компьютера в Azure. Это приводит к увеличению пакета развертывания, отправляемого в Azure. Если по какой бы причине зависимости в файле требований. txt не удается получить с помощью основных средств, для публикации необходимо использовать параметр пользовательские зависимости. 

### <a name="custom-dependencies"></a>Пользовательские зависимости

Если в проекте используются пакеты, которые не являются общедоступными для наших средств, их можно сделать доступными для приложения, поместив их в каталог \_\_приложение\_\_/. python_packages. Перед публикацией выполните следующую команду, чтобы установить зависимости локально:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

При использовании пользовательских зависимостей следует использовать параметр публикации `--no-build`, так как вы уже установили зависимости.  

```command
func azure functionapp publish <APP_NAME> --no-build
```

Не забудьте заменить `<APP_NAME>` именем приложения-функции, размещенного в Azure.

## <a name="unit-testing"></a>Модульное тестирование

Функции, написанные на языке Python, можно тестировать так же, как и другой код Python, используя стандартные платформы тестирования. Для большинства привязок можно создать макетный объект ввода, создав экземпляр соответствующего класса из пакета `azure.functions`. Так как пакет [`azure.functions`](https://pypi.org/project/azure-functions/) недоступен сразу, обязательно установите его с помощью файла `requirements.txt`, как описано в разделе [Управление пакетами](#package-management) выше. 

Например, ниже приведено фиктивное тестирование функции, активируемой HTTP:

```json
{
  "scriptFile": "__init__.py",
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
# __app__/HttpTrigger/__init__.py
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
# tests/test_httptrigger.py
import unittest

import azure.functions as func
from __app__.HttpTrigger import my_function

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

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "python-queue-items",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```python
# __app__/QueueTrigger/__init__.py
import azure.functions as func

def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# tests/test_queuetrigger.py
import unittest

import azure.functions as func
from __app__.QueueTrigger import my_function

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
## <a name="temporary-files"></a>Временные файлы

Метод `tempfile.gettempdir()` возвращает временную папку, которая `/tmp`в Linux. Приложение может использовать этот каталог для хранения временных файлов, создаваемых и используемых вашими функциями во время выполнения. 

> [!IMPORTANT]
> Файлы, записанные во временный каталог, не всегда сохраняются между вызовами. Во время масштабирования временные файлы не являются общими для экземпляров. 

Следующий пример создает именованный временный файл во временном каталоге (`/tmp`):

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()   
   fp = tempfile.NamedTemporaryFile()     
   fp.write(b'Hello world!')              
   filesDirListInTemp = listdir(tempFilePath)     
```   

Рекомендуется хранить тесты в папке отдельно от папки проекта. Это позволяет не развертывать тестовый код в приложении. 

## <a name="known-issues-and-faq"></a>Известные проблемы и часто задаваемые вопросы

Все известные проблемы и запросы возможностей отслеживаются в [списке проблем на GitHub](https://github.com/Azure/azure-functions-python-worker/issues). Если вы столкнулись с проблемой и не можете найти ее решение на GitHub, откройте новую проблему и укажите ее подробное описание.

### <a name="cross-origin-resource-sharing"></a>Общий доступ к ресурсам независимо от источника

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

## <a name="next-steps"></a>Дополнительная информация

Для получения дополнительных сведений см. следующие ресурсы:

* [Документация по API пакета функций Azure](/python/api/azure-functions/azure.functions?view=azure-python)
* [Рекомендации по Функциям Azure](functions-best-practices.md)
* [Azure Functions triggers and bindings (Триггеры и привязки в Функциях Azure)](functions-triggers-bindings.md)
* [Привязки хранилища BLOB-объектов Azure для службы "Функции Azure"](functions-bindings-storage-blob.md)
* [Триггеры и привязки HTTP в службе "Функции Azure"](functions-bindings-http-webhook.md)
* [Привязки хранилища очередей Azure для службы "Функции Azure"](functions-bindings-storage-queue.md)
* [Триггер таймера](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
