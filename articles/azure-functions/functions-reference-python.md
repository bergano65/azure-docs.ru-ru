---
title: Справочник разработчика Python. Функции Azure
description: Сведения о разработке функций на языке Python
ms.topic: article
ms.date: 12/13/2019
ms.openlocfilehash: 30f40db33b6aa8b40202c023f301265565257180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276690"
---
# <a name="azure-functions-python-developer-guide"></a>Справочник разработчика Python. Функции Azure

В этой статье содержатся общие сведения о разработке Функций Azure с помощью Python. Предполагается, что вы уже прочли [руководство для разработчиков Функций Azure](functions-reference.md). 

Для проектов автономных образцов функций в Python см. [Python Functions samples](/samples/browse/?products=azure-functions&languages=python) 

## <a name="programming-model"></a>Модель программирования

Azure Functions ожидает, что функция будет безапозным методом в скрипте Python, который обрабатывает входные данные и производит выход. По умолчанию время выполнения ожидает, что метод будет `main()` реализован `__init__.py` как глобальный метод, называемый в файле. Вы также можете [указать альтернативную точку входа.](#alternate-entry-point)

Данные триггеров и привязок привязаны к функции через атрибуты метода с использованием `name` свойства, определенного в файле *function.json.* Например, _function.json_ ниже описывает простую функцию, `req`вызванную запросом HTTP под названием:

:::code language="son" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

На основе этого `__init__.py` определения файл, содержащий функциональный код, может выглядеть следующим примером:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Вы также можете явно объявить типы атрибутов и тип возврата в функции, используя аннотации типа Python. Это поможет вам использовать функции intellisense и autocomplete, предоставляемые многими редакторами кода Python.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Аннотации Python, включенные в пакет [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python), позволяют привязать входные и выходные данные к методам.

## <a name="alternate-entry-point"></a>Альтернативная точка входа

Вы можете изменить поведение функции по умолчанию, дополнительно указав `scriptFile` и `entryPoint` свойства в файле *function.json.* Например, _ниже функция.json_ указывает время выполнения `customentry()` для использования метода в _файле main.py_ в качестве точки входа для функции Azure.

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

Рекомендуемая структура папок для проекта Python Functions выглядит следующим примером:

```
 __app__
 | - my_first_function
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function
 | | - __init__.py
 | | - function.json
 | - shared_code
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - host.json
 | - requirements.txt
 tests
```
Основная папка проекта\_\_\_\_(приложение) может содержать следующие файлы:

* *local.settings.json*: Используется для хранения настроек приложений и строк соединения при локальном запуске. Этот файл не публикуется в Azure. Чтобы узнать больше, [см.](functions-run-local.md#local-settings-file)
* *requirements.txt*: Содержит список пакетов, которые система устанавливает при публикации в Azure.
* *host.json*: Содержит параметры глобальной конфигурации, которые влияют на все функции в приложении функции. Этот файл не публикуется в Azure. Не все варианты поддерживаются при локальном запуске. Чтобы узнать больше, [см.](functions-host-json.md)
* *.funcignore*: (необязательно) объявляет файлы, которые не должны быть опубликованы в Azure.
* *.gitignore*: (Необязательно) объявляет файлы, которые исключены из git репо, такие как local.settings.json.

У каждой функции есть собственный файл кода и файл конфигурации привязки. 

При развертывании проекта в функциональном приложении в Azure все содержимое основной папки проекта*\_\_(приложение)\_* должно быть включено в пакет, но не в саму папку. В этом примере `tests`рекомендуется вести тесты в папке отдельно от папки проекта. Это удерживает вас от развертывания тестового кода с вашим приложением. Для получения дополнительной [Unit Testing](#unit-testing)информации см.

## <a name="import-behavior"></a>Поведение при импорте

Вы можете импортировать модули в код функции, используя как явные относительные, так и абсолютные ссылки. Основываясь на структуре папки, показанной выше, следующие импортные работы из приложения функционального файла * \_ \_\_\_\_«моя первая\_функция\\»\_init\_\_.py*:

```python
from . import example #(explicit relative)
```

```python
from ..shared_code import my_first_helper_function #(explicit relative)
```

```python
from __app__ import shared_code #(absolute)
```

```python
import __app__.shared_code #(absolute)
```

Следующий импорт *не работает* из одного файла:

```python
import example
```

```python
from example import some_helper_code
```

```python
import shared_code
```

Общий код должен храниться в отдельной папке в * \_ \_приложении.\_* Для ссылки на модули в *общей\_* папке кода можно использовать следующий синтаксис:

```python
from __app__.shared_code import my_first_helper_function
```

## <a name="triggers-and-inputs"></a>Триггеры и входные данные

Входные данные в Функциях Azure делятся на две категории: входные данные триггеров и дополнительные входные данные. Хотя они отличаются `function.json` в файле, использование идентично в коде Python.  Строки подключения или секреты для триггерных и `local.settings.json` входиновых источников отображают значения в файле при локальном запуске и настройки приложения при запуске в Azure. 

Например, следующий код демонстрирует разницу между ними:

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

При активации этой функции HTTP-запрос передается в функцию с помощью `req`. Запись будет извлечена из хранилища Azure Blob на основе _идентификатора_ в URL-адресе маршрута и доступна как `obj` в корпусе функции.  В данном случае указанная учетная запись хранилища — строка соединения, найденная в настройках приложения AzureWebJobsStorage, которая является той же учетной записью хранения, используемой функциональным приложением.


## <a name="outputs"></a>Выходные данные

Выходные данные можно выразить как возвращаемое значение или параметры вывода. Если используется только один вывод, мы рекомендуем использовать возвращаемое значение. Для нескольких выводов нужно использовать параметры вывода.

Чтобы использовать возвращаемое значение функции в качестве значения выходной привязки, присвойте свойству `name` значение `$return` в `function.json`.

Для создания нескольких выходов `set()` используйте [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) метод, предоставляемый интерфейсом, чтобы присвоить значение привязке. Например, следующая функция может направлять сообщение в очередь и возвращает ответ HTTP.

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

Доступ к журналу времени выполнения функций Azure доступен через корневой [`logging`](https://docs.python.org/3/library/logging.html#module-logging) обработчик в приложении функции. Это средство ведения журнала привязано к Application Insights и позволяет отмечать предупреждения и ошибки, возникшие во время выполнения функции.

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

Чтобы узнать больше [Monitor Azure Functions](functions-monitoring.md)о журнале, см.

## <a name="http-trigger-and-bindings"></a>HTTP Триггер и привязки

Триггер HTTP определен в файле function.jon. Привязка `name` должна соответствовать указанному параметру в функции. В предыдущих примерах `req` используется связывающее имя. Этот параметр является объектом [HttpRequest,] и объект [HttpResponse] возвращается.

С объекта [HttpRequest] можно получить заголовки запросов, параметры запроса, параметры маршрута и тело сообщения. 

Ниже приводится следующий пример из [шаблона триггера HTTP для Python.](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python) 

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

В этой функции значение `name` параметра запроса получено из `params` параметра объекта [HttpRequest.] Тело сообщения, закодированное JSON, `get_json` считывается с помощью метода. 

Аналогичным образом можно настроить `status_code` `headers` и для сообщения ответа в возвращенном объекте [HttpResponse.]

## <a name="scaling-and-concurrency"></a>Масштабирование и параллелизм

По умолчанию Azure Functions автоматически отслеживает нагрузку на приложение и создает дополнительные экземпляры хоста для Python по мере необходимости. Функции используют встроенные (не настраиваемые пользователем) пороги для различных типов триггеров, чтобы решить, когда добавлять экземпляры, такие как возраст сообщений и размер очереди для очереди Trigger. Для получения дополнительной [How the Consumption and Premium plans work](functions-scale.md#how-the-consumption-and-premium-plans-work)информации см.

Такое поведение масштабирования достаточно для многих приложений. Однако приложения с любыми из следующих характеристик могут масштабироваться не так эффективно:

- Приложение должно обрабатывать многие параллельные вызовы.
- Приложение обрабатывает большое количество событий ввоза/ввоза.
- Приложение связано между собой.

В таких случаях можно повысить производительность, используя шаблоны async и используя несколько процессов работы на языке.

### <a name="async"></a>Async

Поскольку Python — это однопоните времени выполнения, экземпляр хоста для Python может обрабатывать только одну функцию вызова одновременно. Для приложений, обрабатывающих большое количество событий ввоза и/или связанных вводимых, можно повысить производительность, асинхронно выполнять функции.

Чтобы запустить функцию асинхронно, используйте `async def` оператора, который запускает функцию с [asyncio](https://docs.python.org/3/library/asyncio.html) непосредственно:

```python
async def main():
    await some_nonblocking_socket_io_op()
```

Функция без `async` ключевого слова выполняется автоматически в пуле потоков asyncio:

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Используйте несколько процессов работы языковых

По умолчанию каждый экземпляр хоста Функции имеет единый процесс работы на языке. Можно увеличить количество рабочих процессов на узла (до 10) с помощью настройки [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) приложения. Затем Azure Functions пытается равномерно распределить одновременные вызовы функций между этими сотрудниками. 

В FUNCTIONS_WORKER_PROCESS_COUNT применяется к каждому узлам, который создает Функции при масштабировании приложения для удовлетворения спроса. 

## <a name="context"></a>Контекст

Чтобы получить контекст вызова функции во время [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) выполнения, включите аргумент в его подпись. 

Пример:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

Класс [**контекста**](/python/api/azure-functions/azure.functions.context?view=azure-python) имеет следующие атрибуты строки:

`function_directory`  
Каталог, в котором выполняется функция.

`function_name`  
Имя функции.

`invocation_id`  
Идентификатор текущего вызова функции.

## <a name="global-variables"></a>Глобальные переменные

Не гарантируется, что состояние вашего приложения будет сохранено для будущих выполнения. Однако время выполнения функций Azure часто повторно использует один и тот же процесс для нескольких выполнения одного и того же приложения. Для того, чтобы кэшировать результаты дорогих вычислений, объявить его в качестве глобальной переменной. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Переменные среды

В функциях [параметры приложений,](functions-app-settings.md)такие как строки соединения службы, подвергаются воздействию переменных среды во время выполнения. Вы можете получить доступ `import os` к этим `setting = os.environ["setting-name"]`настройкам, объявив, а затем использовать, .

Следующий пример получает [настройки приложения,](functions-how-to-use-azure-function-app-settings.md#settings)с ключом с именем: `myAppSetting`

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Для локальной разработки настройки приложений [сохраняются в файле local.settings.json.](functions-run-local.md#local-settings-file)  

## <a name="python-version"></a>Версия Python 

Функции Azure поддерживают следующие версии Python:

| Версия службы "Функции" | Версии python<sup>*</sup> |
| ----- | ----- |
| 3.x | 3.8<br/>3,7<br/>3.6 |
| 2.x | 3,7<br/>3.6 |

<sup>*</sup>Официальные дистрибутивы CPython

Чтобы запросить конкретную версию Python при создании приложения `--runtime-version` функции [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) в Azure, используйте опцию команды. Версия запуска функций устанавливается `--functions-version` опцией. Версия Python устанавливается при создании приложения функции и не может быть изменена.  

При локальном запуске время выполнения использует доступную версию Python. 

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

Когда вы будете готовы к публикации, убедитесь, что все ваши общедоступные зависимости перечислены в файле requirements.txt, который находится в корне каталога проекта. 

Файлы проекта и папки, которые исключены из публикации, включая папку виртуальной среды, перечислены в файле .funcignore.

Для публикации проекта Python в Azure поддерживаются три действия сборки:

+ Удаленная сборка: Зависимости создаются удаленно на основе содержимого файла requirements.txt. [Удаленная сборка](functions-deployment-technologies.md#remote-build) является рекомендуемым методом сборки. Удаленный также является опцией сборки по умолчанию инструментария Azure. 
+ Локальная сборка: Зависимости получаются локально на основе содержимого файла requirements.txt. 
+ Пользовательские зависимости: Ваш проект использует пакеты, не доступные нашим инструментам. (Требует докер.)

Чтобы создать свои зависимости и опубликовать с помощью системы непрерывной доставки (CD), [используйте Azure Pipelines.](functions-how-to-azure-devops.md)

### <a name="remote-build"></a>Удаленная сборка

По умолчанию основные инструменты Azure Functions App запрашивают удаленную сборку, когда используется следующий [функционер func azure, публикующий](functions-run-local.md#publish) команду для публикации проекта Python в Azure. 

```bash
func azure functionapp publish <APP_NAME>
```

Не забудьте заменить `<APP_NAME>` именем приложения-функции, размещенного в Azure.

[Расширение функций Azure для Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) также требует удаленной сборки по умолчанию. 

### <a name="local-build"></a>Локальная сборка

Можно предотвратить удаленную сборку, используя следующий [функционер func azure functionapp, публикующий](functions-run-local.md#publish) команду для публикации с помощью локальной сборки. 

```command
func azure functionapp publish <APP_NAME> --build local
```

Не забудьте заменить `<APP_NAME>` именем приложения-функции, размещенного в Azure. 

Используя `--build local` опцию, зависимости проекта считываются из файла requirements.txt, и эти зависимые пакеты загружаются и устанавливаются локально. Файлы и зависимости проекта развертываются с локального компьютера в Azure. Это приводит к загрузке большего пакета развертывания в Azure. Если по какой-либо причине зависимости в файле requirements.txt не могут быть приобретены Core Tools, необходимо использовать опцию пользовательских зависимостей для публикации. 

### <a name="custom-dependencies"></a>Пользовательские зависимости

Если ваш проект использует пакеты, не доступные нашим инструментам, вы можете \_ \_сделать их доступными для вашего приложения, поместив их в каталог приложения\_\_/.python_packages. Перед публикацией запустите следующую команду для установки локальных зависимостей:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

При использовании пользовательских зависимостей `--no-build` следует использовать опцию публикации, так как вы уже установили зависимости.  

```command
func azure functionapp publish <APP_NAME> --no-build
```

Не забудьте заменить `<APP_NAME>` именем приложения-функции, размещенного в Azure.

## <a name="unit-testing"></a>Модульное тестирование

Функции, написанные в Python, могут быть протестированы, как и другие коды Python, используя стандартные платформы тестирования. Для большинства привязок можно создать макет ввода объекта, создав экземпляр соответствующего `azure.functions` класса из пакета. Поскольку [`azure.functions`](https://pypi.org/project/azure-functions/) пакет не сразу доступен, не `requirements.txt` забудьте установить его через файл, как описано в разделе [управления пакетами](#package-management) выше. 

Например, ниже приводится макет теста функции http срабатывает:

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

Вот еще один пример, с функцией срабатывания очереди:

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
## <a name="temporary-files"></a>"Временные файлы"

Метод `tempfile.gettempdir()` возвращает временную папку, которая `/tmp`на Linux . Приложение может использовать этот каталог для хранения временных файлов, генерируемых и используемых вашими функциями во время выполнения. 

> [!IMPORTANT]
> Файлы, написанные во временный каталог, не гарантированно сохраняются в разных призывах. Во время масштабирования временные файлы не передаются между экземплярами. 

Следующий пример создает названный временный файл`/tmp`во временном каталоге ( ):

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

Мы рекомендуем вам поддерживать тесты в папке отдельно от папки проекта. Это удерживает вас от развертывания тестового кода с вашим приложением. 

## <a name="known-issues-and-faq"></a>Известные проблемы и часто задаваемые вопросы

Все известные проблемы и запросы возможностей отслеживаются в [списке проблем на GitHub](https://github.com/Azure/azure-functions-python-worker/issues). Если вы столкнулись с проблемой и не можете найти ее решение на GitHub, откройте новую проблему и укажите ее подробное описание.

### <a name="cross-origin-resource-sharing"></a>Предоставление общего доступа к ресурсам независимо от источника

Функции Azure поддерживают совместное использование ресурсов совместного использования ресурсов совместного использования ресурсов (CORS). CORS настроен [на портале](functions-how-to-use-azure-function-app-settings.md#cors) и через [Azure CLI](/cli/azure/functionapp/cors). Список разрешенных исходов CORS применяется на уровне приложения функции. С включенным CORS `Access-Control-Allow-Origin` ответы включают заголовок. Дополнительные сведения см. в разделе [Общий доступ к ресурсам независимо от источника](functions-how-to-use-azure-function-app-settings.md#cors).

Список разрешенных [истоков в настоящее время не поддерживается](https://github.com/Azure/azure-functions-python-worker/issues/444) в функциональных приложениях Python. Из-за этого ограничения необходимо четко `Access-Control-Allow-Origin` установить заголовок в функциях HTTP, как показано в следующем примере:

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

Убедитесь, что вы также обновить function.json для поддержки метода OPTIONS HTTP:

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

Этот метод HTTP используется веб-браузерами для согласования списка разрешенных истоков. 

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в следующих ресурсах:

* [Документация API пакета функций Azure](/python/api/azure-functions/azure.functions?view=azure-python)
* [Рекомендации по функциям Azure](functions-best-practices.md)
* [Триггеры и привязки функций Azure](functions-triggers-bindings.md)
* [Привязки хранилища BLOB-объектов Azure для службы "Функции Azure"](functions-bindings-storage-blob.md)
* [Триггеры и привязки HTTP в службе "Функции Azure"](functions-bindings-http-webhook.md)
* [Привязки хранилища очередей Azure для службы "Функции Azure"](functions-bindings-storage-queue.md)
* [Триггер таймера](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
