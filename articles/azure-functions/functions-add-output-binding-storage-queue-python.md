---
title: Добавление привязки очереди службы хранилища Azure к функции Python
description: Интеграция очереди службы хранилища Azure с функцией Python с использованием выходной привязки.
ms.date: 01/15/2020
ms.topic: quickstart
ms.openlocfilehash: 6cea44dca666bbf002de6e2b7dd283f49ac7bd5a
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485171"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Добавление привязки очереди службы хранилища Azure к функции Python

В этой статье вы узнаете, как интегрировать очередь службы хранилища Azure с функцией и учетной записью хранения, созданной в кратком руководстве [Создание функции Python, активируемой HTTP](functions-create-first-function-python.md). Чтобы реализовать такую интеграцию, используется *выходная привязка*, которая записывает в сообщение очереди данные из HTTP-запроса. Выполнение шагов из этой статьи не влечет за собой никаких дополнительных затрат, помимо нескольких центов США, потраченных при выполнении предыдущего краткого руководства.

## <a name="prerequisites"></a>Предварительные требования

- Выполните краткое руководство [Создание функции Python, активируемой HTTP](functions-create-first-function-python.md). Если вы уже удалили ресурсы по окончании работы по этой статье, выполните шаги еще раз, чтобы повторно создать приложение-функцию в Azure, но оставьте ресурсы.

## <a name="retrieve-the-azure-storage-connection-string"></a>Получение строки подключения к службе хранилища Azure

Когда в предыдущей статье вы создавали приложение-функцию, вы также создали учетную запись хранения в Azure. Строка подключения данной учетной записи надежно хранится в параметрах приложения в Azure. Скачав параметр в файл *local.settings.json*, вы можете использовать это подключение для записи данных в очередь службы хранилища в той же учетной записи при локальном запуске функции. 

1. В корневом каталоге проекта выполните следующую команду, заменив `<app_name>` именем приложения-функции из предыдущего краткого руководства. Эта команда перезапишет все существующие значения в файле.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Откройте файл *local.settings.json* и найдите значение `AzureWebJobsStorage`, которое является строкой подключения к учетной записи хранения. В других разделах этой статьи вы будете использовать имя `AzureWebJobsStorage` и строку подключения.

> [!IMPORTANT]
> Файл *local.settings.json* содержит секреты, скачанные из Azure, поэтому всегда исключайте этот файл из системы управления версиями. Файл *.gitignore*, созданный с помощью локального проекта функций, по умолчанию исключает файл.

## <a name="add-an-output-binding-to-functionjson"></a>Добавление выходной привязки в файл function.json

У функции может быть только один триггер, но у нее может быть несколько входных и выходных привязок, которые позволяют подключаться к другим службам и ресурсам Azure без написания пользовательского кода интеграции. Эти привязки объявляются в файле *function.json* в папке функции в соответствии с языком, который используется для функции.

В предыдущем кратком руководстве файл *function.json* в папке *HttpExample* содержит две привязки в коллекции `bindings`:

```json
{
  "scriptFile": "__init__.py",
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

У каждой привязки есть по крайней мере такие параметры, как тип, направление и имя. В примере выше у первой привязки тип `httpTrigger` и направление `in`. Для направления `in` `name` указывает имя входного параметра, который отправляется в функцию, когда ее вызывает триггер.

Вторая привязка относится к типу `http` с направлением `out`. В этом случае специальный параметр `name` `$return` указывает, что эта привязка использует возвращаемое значение функции, а не предоставляет входной параметр.

Чтобы записать в очередь службы хранилища Azure данные из этой функции, добавьте привязку `out` типа `queue` с именем `msg`, как показано в коде ниже:

```json
{
  "scriptFile": "__init__.py",
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
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

В этом случае `msg` передается функции в качестве выходного аргумента. Для типа `queue` необходимо также указать имя очереди в `queueName` и указать *имя* подключения к службе хранилища Azure (из *local.settings.json*) в `connection`.

Дополнительные сведения о привязках см. в статье [Azure Functions triggers and bindings](functions-triggers-bindings.md) (Основные понятия триггеров и привязок в Функциях Azure) и в разделе о [конфигурации выходных данных очереди](functions-bindings-storage-queue-output.md#configuration).

## <a name="add-code-to-use-the-output-binding"></a>Добавление кода для использования выходной привязки

С помощью привязки очереди, указываемой в файле *function.json*, теперь можно обновлять функцию, чтобы она получала выходной параметр `msg` и записывала сообщения в очередь.

Обновите *HttpExample\\\_\_init\_\_.py* в соответствии с кодом ниже, добавив параметр `msg` в определение функции и `msg.set(name)` для оператора `if name:`.

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

Параметр `msg` — это экземпляр [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest). Его метод `set` записывает строковое сообщение в очередь. В этом случае имя передается функции в строке запроса URL-адреса.

Обратите внимание, что *не* нужно писать код для проверки подлинности, получения ссылки на очередь или записи данных. Все эти задачи интеграции удобно осуществляются в среде выполнения Функций Azure и с использованием выходной привязки очереди.

## <a name="run-and-test-the-function-locally"></a>Локальные запуск и проверка функции

1. В терминале или командной строке перейдите в папку проекта функций *LocalFunctionProj*.

1. Запустите хост-приложение среды выполнения Функций Azure, выполнив следующую команду:

    ```
    func host start
    ```

1. Когда запуск завершится и вы увидите URL-адрес конечной точки `HttpExample`, скопируйте его в браузер и добавьте строку запроса `?name=<your-name>`, сформировав полный URL-адрес, например `http://localhost:7071/api/HttpExample?name=Guido`. В браузере должно отобразиться сообщение, например `Hello Guido`, как в предыдущей статье.

    Если конечная точка `HttpExample` не отображается, закройте узел, используя клавиши **Ctrl**+**C**, и проверьте выходные данные на наличие ошибок. Например, узел не активирует конечную точку, если в *function.json* есть ошибка. Убедитесь также, что `func host start` выполняется из папки проекта функций, а не из папки *HttpExample*.

1. Когда все будет готово, завершите работу узла, нажав клавиши **Ctrl**+**C**.

> [!TIP]
> Во время запуска узел скачивает и устанавливает [расширение привязки хранилища](functions-bindings-storage-blob.md#add-to-your-functions-app) и другие расширения привязки Майкрософт. Эта установка происходит, потому что расширения привязки включены по умолчанию в файле *host.json* со следующими свойствами:
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> Если возникнут ошибки, связанные с расширениями привязки, убедитесь, что указанные выше свойства есть в файле *host.json*.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Просмотр сообщения в очереди службы хранилища Azure

Когда функция создает HTTP-ответ для веб-браузера, она также вызывает `msg.set(name)`, который записывает сообщение в очередь службы хранилища Azure с именем `outqueue`, как указано в привязке очереди. Очередь можно просмотреть на [портале Azure](../storage/queues/storage-quickstart-queues-portal.md) или в [Обозревателе службы хранилища Azure](https://storageexplorer.com/). Очередь также можно просмотреть в интерфейсе командной строки Azure. Для этого выполните приведенные ниже шаги.

1. Откройте файл *local.setting.json* проекта функций и скопируйте значение строки подключения. В окне терминала или командной строки выполните следующую команду, чтобы создать переменную среды с именем `AZURE_STORAGE_CONNECTION_STRING`. Вставьте конкретную строку подключения вместо `<connection_string>`. (Эта переменная среды означает, что вам не нужно указывать строку подключения для каждой последующей команды с помощью аргумента `--connection-string`.)

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. (Дополнительно) Команду [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) можно использовать для просмотра очередей службы хранилища в учетной записи. В выходных данных этой команды должна быть очередь с именем `outqueue`, созданная при написании функцией первого сообщения в этой очереди.
    
    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    az storage queue list --output tsv
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    az storage queue list --output tsv
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    az storage queue list --output tsv
    ```
    
    ---


1. Используйте команду [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek), чтобы просматривать сообщения в этой очереди. Необходимо указывать первое имя, использованное ранее при проверке функции. Команда получает первое сообщение в очереди в [кодировке Base64](functions-bindings-storage-queue-trigger.md#encoding), поэтому необходимо также декодировать сообщение, чтобы просмотреть его в виде текста.

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    Коллекцию сообщений требуется разыменовать и раскодировать из base64, поэтому запустите PowerShell и выполните соответствующую команду PowerShell.

    ---
    
## <a name="redeploy-the-project-to-azure"></a>Повторное развертывание проекта в Azure

Теперь, когда вы протестировали функцию локально и убедились, что она записала сообщение в очередь службы хранилища Azure, можно повторно развернуть проект, чтобы обновить конечную точку, работающую в Azure.

1. В папке *LocalFunctionsProj* используйте команду [`func azure functionapp publish`](functions-run-local.md#project-file-deployment), чтобы повторно развернуть проект, заменив `<app_name>` именем приложения.

    ```
    func azure functionapp publish <app_name>
    ```
    
1. Как и в предыдущем кратком руководстве, используйте браузер или cURL для проверки повторно развернутой функции.

    # <a name="browser"></a>[Браузер](#tab/browser)
    
    Скопируйте полный URL-адрес вызова **Invoke url**, показанный в выходных данных команды publish, в адресную строку браузера, добавив параметр запроса `&name=Azure`. В браузере должны отображаться выходные данные, аналогичные данным при локальном запуске функции.

    ![Выходные данные функции, выполняемой в Azure в браузере](./media/functions-create-first-function-python/function-test-cloud-browser.png)

    # <a name="curl"></a>[curl](#tab/curl)
    
    Запустите [cURL](https://curl.haxx.se/), используя **Invoke url** и добавив параметр `&name=Azure`. Результатом выполнения этой команды должен быть текст "Hello Azure".
    
    ![Выходные данные функции, выполненной в Azure в cURL](./media/functions-create-first-function-python/function-test-cloud-curl.png)

    --- 

1. Проверьте очередь службы хранилища еще раз, как описано в предыдущем разделе, чтобы убедиться, что в ней содержится новое сообщение, записанное в очередь.

    Если вы используете интерфейс командной строки Azure для проверки очереди, команда `az storage message peek` покажет только первое сообщение в очереди. Чтобы смоделировать обработку сообщений, используйте вместо нее команду `az storage message get` с теми же аргументами. Команда `get` возвратит сообщение и удалит его из очереди. Эту команду можно повторять до тех пор, пока очередь не будет пуста (и команда не выдаст ошибку).

## <a name="clean-up-resources"></a>Очистка ресурсов

После перехода к следующему шагу, [Включение интеграции с Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource), вам потребуется сохранить все ресурсы, чтобы использовать их в будущем.

В противном случае используйте следующую команду, чтобы удалить группу ресурсов и все содержащиеся в ней ресурсы и избежать дополнительных расходов.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Включение интеграции Application Insights с приложением Функций Azure](functions-monitoring.md#manually-connect-an-app-insights-resource)

Другие ресурсы:

- [Примеры полных проектов Функций на Python](/samples/browse/?products=azure-functions&languages=python).
- [Azure Functions Python Developer Guide](functions-reference-python.md) (Справочник по Функциям Azure для разработчика Python)
- [Azure Functions triggers and bindings](functions-triggers-bindings.md) (Основные понятия триггеров и привязок в Функциях Azure)
- [Страница цен на Функции Azure](https://azure.microsoft.com/pricing/details/functions/)
- Статья [Estimating Consumption plan costs](functions-consumption-costs.md) (Оценка затрат на план потребления).
