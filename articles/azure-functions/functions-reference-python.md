---
title: Справочник разработчика Python. Функции Azure
description: Сведения о разработке функций на языке Python
ms.topic: article
ms.date: 12/13/2019
ms.custom: devx-track-python
ms.openlocfilehash: 776355ce981ba5cc2a24bfe473da2f55427eadf6
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87850752"
---
# <a name="azure-functions-python-developer-guide"></a>Справочник разработчика Python. Функции Azure

В этой статье содержатся общие сведения о разработке Функций Azure с помощью Python. Предполагается, что вы уже прочли [руководство для разработчиков Функций Azure](functions-reference.md).

Отдельные примеры проектов Функций в Python см. в [этой статье](/samples/browse/?products=azure-functions&languages=python).

## <a name="programming-model"></a>Модель программирования

Функция Azure должна быть реализована как метод без отслеживания состояния в сценарии Python, который обрабатывает входные данные и создает выходные данные. По умолчанию среда выполнения ожидает, что метод реализован как глобальный метод с именем `main()` в файле `__init__.py`. Также можно [указать альтернативную точку входа](#alternate-entry-point).

Данные из триггеров и привязок будут привязаны к функции через атрибуты метода с помощью свойства `name`, определяемого в файле *function.json*. Пример файла _function.json_ ниже описывает простую функцию с именем `req`, которая активируется HTTP-запросом.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

На основе этого определения файл `__init__.py`, содержащий код функции, может выглядеть, как в следующем примере:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Также можно явно объявить для функции типы атрибутов и тип возвращаемого значения с помощью аннотации типов Python. Это помогает использовать функции IntelliSense и автозаполнения, предоставляемые во многих редакторах кода Python.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Аннотации Python, включенные в пакет [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python), позволяют привязать входные и выходные данные к методам.

## <a name="alternate-entry-point"></a>Альтернативная точка входа

Можно изменить поведение функции по умолчанию, при необходимости указав свойства `scriptFile` и `entryPoint` в файле *function.json*. Пример файла _function.json_ ниже указывает, что среда выполнения должна использовать метод `customentry()` из файла _main.py_ в качестве точки входа для Функции Azure.

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

Рекомендуемая структура папок для проекта Функций на Python выглядит следующим образом:

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
 | - Dockerfile
 tests
```
В главной папке проекта (\_\_app\_\_) могут содержаться следующие файлы:

* *local.settings.json*: используется для хранения параметров приложения и строк подключения при локальном выполнении. Этот файл не публикуется в Azure. Дополнительные сведения см. в разделе [local.settings.file](functions-run-local.md#local-settings-file).
* *requirements.txt*: содержит список пакетов, которые должны быть установлены при публикации в Azure.
* *host.json*: содержит параметры глобальной конфигурации, влияющие на все функции в приложении-функции. Этот файл не публикуется в Azure. При локальном запуске поддерживаются не все параметры. Дополнительные сведения см. в разделе [host.json](functions-host-json.md).
* *.funcignore*: (необязательно) объявляет файлы, которые не должны публиковаться в Azure.
* *.gitignore*: (необязательно) объявляет файлы, исключаемые из репозитория Git, например local.settings.json.
* *Dockerfile*: (необязательно) используется при публикации проекта в [настраиваемом контейнере](functions-create-function-linux-custom-image.md).

У каждой функции есть собственный файл кода и файл конфигурации привязки.

При развертывании проекта в приложении-функции Azure следует включить в пакет все содержимое главной папки ( *\_\_app\_\_* ), но не саму эту папку. Тесты рекомендуется хранить в папке, отдельной от папки проекта (в этом примере — `tests`). Это позволяет не развертывать тестовый код в приложении. Дополнительные сведения см. в разделе [Модульное тестирование](#unit-testing).

## <a name="import-behavior"></a>Поведение при импорте

Вы можете импортировать модули в код функции, используя явные относительные и абсолютные ссылки. В соответствии со структурой папок, показанной выше, следующие операции импорта работают в файле функции *\_\_app\_\_\my\_first\_function\\_\_init\_\_.py*:

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

Следующие операции импорта *не работают* в том же файле:

```python
import example
```

```python
from example import some_helper_code
```

```python
import shared_code
```

Общий код следует располагать в отдельной папке в папке *\_\_app\_\_* . Для ссылок на модули в папке *shared\_code* можно использовать следующий синтаксис:

```python
from __app__.shared_code import my_first_helper_function
```

## <a name="triggers-and-inputs"></a>Триггеры и входные данные

Входные данные в Функциях Azure делятся на две категории: входные данные триггеров и дополнительные входные данные. Они по-разному представлены в файле `function.json`, но использование в коде Python полностью идентично.  Строки подключения или секреты для триггеров и источников входных данных сопоставляются со значениями в файле `local.settings.json` при выполнении в локальной среде и с параметрами приложения при выполнении в Azure.

В следующем примере кода показано различие:

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

При активации этой функции HTTP-запрос передается в функцию с помощью `req`. Запись извлекается из хранилища BLOB-объектов Azure по значению _ID_, включенному в URL-адрес маршрута, и становится доступной в виде `obj` в тексте функции.  Здесь указанная учетная запись хранения является строкой подключения в параметре приложения AzureWebJobsStorage, которая является той же учетной записью хранения, используемой приложением-функцией.


## <a name="outputs"></a>Выходные данные

Выходные данные можно выразить как возвращаемое значение или параметры вывода. Если используется только один вывод, мы рекомендуем использовать возвращаемое значение. Для нескольких выводов нужно использовать параметры вывода.

Чтобы использовать возвращаемое значение функции в качестве значения выходной привязки, присвойте свойству `name` значение `$return` в `function.json`.

Чтобы создать несколько выходных значений, используйте метод `set()` из интерфейса [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python), чтобы присвоить значение привязке. Например, следующая функция может направлять сообщение в очередь и возвращает ответ HTTP.

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

## <a name="logging"></a>Logging

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

Дополнительные сведения о ведении журналов см. в статье [Мониторинг Функций Azure](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>Триггеры и привязки HTTP

Триггер HTTP определяется в function.jsфайла. `name` привязки должен соответствовать именованному параметру в функции.
В предыдущих примерах используется имя привязки `req`. Этот параметр является объектом [HttpRequest], и возвращается объект [HttpResponse].

Из объекта [HttpRequest] можно получить заголовки запроса, параметры запроса, параметры маршрута и текст сообщения.

Следующий пример относится к [шаблону триггера HTTP для Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python).

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

В этой функции значение параметра запроса `name` получено из параметра `params` объекта [HttpRequest]. Текст сообщения в кодировке JSON считывается с помощью метода `get_json`.

Аналогичным образом можно задать `status_code` и `headers` для ответного сообщения в возвращенном объекте [HttpResponse].

## <a name="scaling-and-concurrency"></a>Масштабирование и параллелизм

По умолчанию Функции Azure автоматически отслеживают нагрузку на приложение и при необходимости создают дополнительные экземпляры узлов для Python. Функции используют встроенные пороговые значения (недоступные для настройки пользователем) для различных типов триггеров, чтобы решить, когда следует добавлять экземпляры (например, возраст сообщений и размер очереди для QueueTrigger). Дополнительные сведения см. в статье, [посвященной планам с оплатой по мере использования и планам "Премиум"](functions-scale.md#how-the-consumption-and-premium-plans-work).

Такого поведения при масштабировании достаточно для многих приложений. Однако приложения со следующими характеристиками могут масштабироваться не так эффективно:

- Приложение должно обрабатывать много одновременных вызовов.
- Приложение обрабатывает большое количество событий ввода-вывода.
- Приложение привязано к вводу-выводу.

В таких случаях повысить производительность можно путем применения асинхронных шаблонов и использования многоязыковых рабочих процессов.

### <a name="async"></a>Асинхронный режим

Поскольку Python является однопотоковой средой выполнения, экземпляр узла для Python может одновременно обрабатывать только один вызов функции. Для приложений, обрабатывающих большое количество событий ввода-вывода и/или связанных с вводом-выводом, можно повысить производительность, выполняя функции асинхронно.

Чтобы выполнить функцию в асинхронном режиме, используйте инструкцию `async def`, которая запускает функцию с [asyncio](https://docs.python.org/3/library/asyncio.html) напрямую:

```python
async def main():
    await some_nonblocking_socket_io_op()
```

Функция без ключевого слова `async` выполняется автоматически в пуле потоков asyncio:

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Использование многоязыковых рабочих процессов

По умолчанию каждый экземпляр узла функций имеет рабочий процесс с одним языком. Вы можете увеличить количество рабочих процессов на узел (до 10) с помощью параметра приложения [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count). Затем Функции Azure пытаются равномерно распределять одновременные вызовы функций между этими рабочими процессами.

FUNCTIONS_WORKER_PROCESS_COUNT применяется к каждому узлу, создаваемому функциями при масштабировании приложения для удовлетворения потребности.

## <a name="context"></a>Контекст

Чтобы получить контекст вызова функции во время выполнения, включите в его подпись аргумент [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python).

Пример:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

Класс [**Context**](/python/api/azure-functions/azure.functions.context?view=azure-python) имеет следующие строковые атрибуты:

`function_directory` Каталог, в котором выполняется функция.

`function_name` Имя функции.

`invocation_id` Идентификатор текущего вызова функции.

## <a name="global-variables"></a>Глобальные переменные

Не гарантируется, что состояние приложения будет сохранено для будущих выполнений. Однако среда выполнения Функций Azure часто использует один и тот же процесс для нескольких выполнений одного и того же приложения. Чтобы кэшировать результаты ресурсоемких вычислений, объявите ее как глобальную переменную.

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Переменные среды

В Функциях [параметры приложения](functions-app-settings.md), такие как строки подключения службы, доступны в виде переменных среды во время выполнения. Доступ к этим параметрам можно получить, объявив `import os`, а затем воспользовавшись `setting = os.environ["setting-name"]`.

В следующем примере показано получение [параметра приложения](functions-how-to-use-azure-function-app-settings.md#settings) с ключом с именем `myAppSetting`:

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Для локальной разработки параметры приложения [хранятся в файле local.settings.json](functions-run-local.md#local-settings-file).

## <a name="python-version"></a>Версия Python

Функции Azure поддерживают следующие версии Python:

| Версия службы "Функции" | Версии<sup>*</sup> Python |
| ----- | ----- |
| 3.x | 3.8<br/>3,7<br/>3.6 |
| 2.x | 3,7<br/>3.6 |

<sup>*</sup>Официальные дистрибутивы CPython

Чтобы запросить конкретную версию Python при создании приложения-функции в Azure, используйте параметр `--runtime-version` команды [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create). Версия среды выполнения функций задается параметром `--functions-version`. Версия Python задается при создании приложения-функции и изменить ее невозможно.

При локальном запуске среда выполнения использует доступную версию Python.

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

Когда все будет готово к публикации, убедитесь, что все общедоступные зависимости перечислены в файле требований requirements.txt, который находится в корне каталога проекта.

Файлы и папки проекта, исключаемые из публикации, включая папку виртуальной среды, перечислены в файле .funcignore.

Для публикации проекта Python в Azure поддерживаются три действия сборки: удаленная сборка, локальная сборка и сборки с использованием пользовательских зависимостей.

Вы также можете использовать Azure Pipelines для создания зависимостей и публикации с помощью непрерывной поставки (CD). Дополнительные сведения см. в статье [непрерывная поставка с помощью Azure DevOps](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Удаленная сборка

При использовании удаленной сборки зависимости, восстановленные на сервере и в машинных зависимостях, соответствуют рабочей среде. Это приводит к уменьшению размера пакета развертывания. Используйте удаленную сборку при разработке приложений Python в Windows. Если в проекте есть пользовательские зависимости, можно [использовать удаленную сборку с дополнительным URL-адресом индекса](#remote-build-with-extra-index-url).

Зависимости получаются удаленно на основе содержимого файла requirements.txt. В качестве рекомендуемого метода сборки рекомендуется использовать [удаленную сборку](functions-deployment-technologies.md#remote-build). По умолчанию Azure Functions Core Tools запрашивает удаленную сборку при использовании следующей команды [func azure functionapp publish](functions-run-local.md#publish) для публикации проекта Python в Azure.

```bash
func azure functionapp publish <APP_NAME>
```

Не забудьте заменить `<APP_NAME>` именем приложения-функции, размещенного в Azure.

[Расширение Функций Azure для Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) также запрашивает удаленную сборку по умолчанию.

### <a name="local-build"></a>Локальная сборка

Зависимости получаются локально на основе содержимого файла requirements.txt. Вы можете запретить удаленную сборку, используя следующую команду [func azure functionapp publish](functions-run-local.md#publish) для публикации с локальной сборкой.

```command
func azure functionapp publish <APP_NAME> --build local
```

Не забудьте заменить `<APP_NAME>` именем приложения-функции, размещенного в Azure.

С помощью параметра `--build local` зависимости проекта считываются из файла требований requirements.txt, и эти зависимые пакеты загружаются и устанавливаются локально. Файлы проекта и зависимости развертываются с локального компьютера в Azure. Это приводит к увеличению пакета развертывания, отправляемого в Azure. Если по какой-то причине зависимости в файле requirements.txt не удается получить с помощью основных инструментов, для публикации необходимо использовать настраиваемые зависимости.

При локальной разработке в Windows не рекомендуется использовать локальные сборки.

### <a name="custom-dependencies"></a>Настраиваемые зависимости.

Если проект содержит зависимости, не найденные в [индексе пакета Python](https://pypi.org/), существует два способа построения проекта. Метод сборки зависит от способа сборки проекта.

#### <a name="remote-build-with-extra-index-url"></a>Удаленная сборка с дополнительным URL-адресом индекса

Если пакеты доступны из доступного индекса настраиваемого пакета, используйте удаленную сборку. Перед публикацией обязательно [Создайте параметр приложения](functions-how-to-use-azure-function-app-settings.md#settings) с именем `PIP_EXTRA_INDEX_URL` . Значение этого параметра — это URL-адрес пользовательского индекса пакета. Использование этого параметра указывает, что удаленная сборка будет выполняться `pip install` с `--extra-index-url` параметром. Дополнительные сведения см. в [документации по установке для Python PIP](https://pip.pypa.io/en/stable/reference/pip_install/#requirements-file-format).

Вы также можете использовать учетные данные обычной проверки подлинности с дополнительными URL-адресами индексов пакетов. Дополнительные сведения см. в разделе [основные учетные данные проверки подлинности](https://pip.pypa.io/en/stable/user_guide/#basic-authentication-credentials) в документации по Python.

#### <a name="install-local-packages"></a>Установка локальных пакетов

Если в проекте используются пакеты, которые не являются общедоступными для наших инструментов, их можно сделать доступными для приложения, поместив их в каталог \_\_app\_\_/.python_packages. Перед публикацией выполните следующую команду, чтобы установить зависимости локально:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

При использовании настраиваемых зависимостей следует использовать `--no-build` параметр публикации, так как зависимости уже установлены в папке проекта.

```command
func azure functionapp publish <APP_NAME> --no-build
```

Не забудьте заменить `<APP_NAME>` именем приложения-функции, размещенного в Azure.

## <a name="unit-testing"></a>Модульное тестирование

Функции, написанные на языке Python, можно тестировать так же, как и другой код Python, используя стандартные платформы тестирования. Для большинства привязок можно создать макет объекта ввода, создав экземпляр соответствующего класса из пакета `azure.functions`. Поскольку пакет [`azure.functions`](https://pypi.org/project/azure-functions/) может быть недоступен, обязательно установите его с помощью файла `requirements.txt`, как описано в разделе [Управление пакетами](#package-management) выше.

Например, ниже приведена имитация тестирования функции для триггеров HTTP:

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

Вот еще один пример с функцией для триггеров очереди:

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

Метод `tempfile.gettempdir()` возвращает временную папку, которая в Linux — `/tmp`. Приложение может использовать этот каталог для хранения временных файлов, создаваемых и используемых вашими функциями во время выполнения.

> [!IMPORTANT]
> Файлы, записанные во временный каталог, не всегда сохраняются между вызовами. Во время масштабирования временные файлы не используются совместно экземплярами.

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

Тесты рекомендуется хранить в папке, отдельной от папки проекта. Это позволяет не развертывать тестовый код в приложении.

## <a name="preinstalled-libraries"></a>Предустановленные библиотеки

Существует несколько библиотек со средой выполнения функций Python.

### <a name="python-standard-library"></a>Стандартная библиотека Python

Стандартная библиотека Python содержит список встроенных модулей Python, поставляемых вместе с каждым дистрибутивом Python. Большинство этих библиотек помогают получить доступ к функциональным возможностям системы, таким как файловый ввод-вывод. В системах Windows эти библиотеки устанавливаются с Python. В системах на базе UNIX они предоставляются коллекциями пакетов.

Чтобы просмотреть полные сведения о списке этих библиотек, перейдите по ссылкам ниже:

* [Стандартная библиотека Python 3,6](https://docs.python.org/3.6/library/)
* [Стандартная библиотека Python 3,7](https://docs.python.org/3.7/library/)
* [Стандартная библиотека Python 3,8](https://docs.python.org/3.8/library/)

### <a name="azure-functions-python-worker-dependencies"></a>Зависимости рабочих ролей Python в функциях Azure

Работнику функций Python требуется конкретный набор библиотек. Эти библиотеки также можно использовать в функциях, но они не являются частью стандарта Python. Если функции зависят от этих библиотек, они могут быть недоступны коду при выполнении вне функций Azure. Подробный список зависимостей можно найти в разделе **Установка \_ требуется** в файле [Setup.py](https://github.com/Azure/azure-functions-python-worker/blob/dev/setup.py#L282) .

> [!NOTE]
> Если requirements.txt приложения функции содержит `azure-functions-worker` запись, удалите ее. Работник функций автоматически управляется платформой функций Azure и регулярно обновляется новыми функциями и исправлениями ошибок. Ручная установка старой версии рабочей роли в requirements.txt может привести к непредвиденным проблемам.

### <a name="azure-functions-python-library"></a>Библиотека Python для функций Azure

Каждое обновление рабочей роли Python включает новую версию [библиотеки Python для функций Azure (Azure. functions)](https://github.com/Azure/azure-functions-python-library). Такой подход упрощает непрерывное обновление приложений-функций Python, так как каждое обновление имеет обратную совместимость. Список выпусков этой библиотеки можно найти в [Azure-функциях PyPi](https://pypi.org/project/azure-functions/#history).

Версия библиотеки среды выполнения исправлена в Azure и не может быть переопределена requirements.txt. `azure-functions`Запись в requirements.txt относится только к linting и осведомленности клиентов.

Используйте следующий код для отслеживания фактической версии библиотеки функций Python в среде выполнения:

```python
getattr(azure.functions, '__version__', '< 1.2.1')
```

### <a name="runtime-system-libraries"></a>Системные библиотеки среды выполнения

Список предустановленных системных библиотек в образах DOCKER Worker в Python см. по следующим ссылкам:

|  Среда выполнения функций  | Версия Debian | Версии Python |
|------------|------------|------------|
| Версия 2.x | Stretch  | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python37/python37.Dockerfile) |
| Версия 3.x | бустер | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python37/python37.Dockerfile)<br />[Python 3.8](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python38/python38.Dockerfile) |

## <a name="cross-origin-resource-sharing"></a>Предоставление общего доступа к ресурсам независимо от источника

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

CORS полностью поддерживается для приложений-функций Python.

## <a name="known-issues-and-faq"></a>Известные проблемы и часто задаваемые вопросы

Ниже приведен список руководств по устранению распространенных проблем.

* [ModuleNotFoundError и ImportError](recover-python-functions.md#troubleshoot-modulenotfounderror)
* [Не удается импортировать "цигрпк"](recover-python-functions.md#troubleshoot-cannot-import-cygrpc)

Все известные проблемы и запросы возможностей отслеживаются в [списке проблем на GitHub](https://github.com/Azure/azure-functions-python-worker/issues). Если вы столкнулись с проблемой и не можете найти ее решение на GitHub, откройте новую проблему и укажите ее подробное описание.

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительных сведений см. следующие ресурсы:

* [Документация по API пакета Функций Azure](/python/api/azure-functions/azure.functions?view=azure-python)
* [Рекомендации по функциям Azure](functions-best-practices.md)
* [Azure Functions triggers and bindings (Триггеры и привязки в Функциях Azure)](functions-triggers-bindings.md)
* [Привязки хранилища BLOB-объектов Azure для службы "Функции Azure"](functions-bindings-storage-blob.md)
* [Триггеры и привязки HTTP в службе "Функции Azure"](functions-bindings-http-webhook.md)
* [Привязки хранилища очередей Azure для службы "Функции Azure"](functions-bindings-storage-queue.md)
* [Триггер таймера](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
