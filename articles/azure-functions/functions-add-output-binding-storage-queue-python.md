---
title: Добавление привязки очереди службы хранилища Azure к функции Python
description: Сведения о добавлении выходной привязки очереди службы хранилища Azure к функции Python с помощью Azure CLI и Azure Functions Core Tools.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: aaeee4238110faa7a842073af8431b30b885db3c
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870025"
---
# <a name="add-an-azure-storage-queue-binding-to-your-function"></a>Добавление привязки очереди службы хранилища Azure к функции

Функции Azure позволяют выполнять подключение служб Azure и других ресурсов к функциям без необходимости написания кода для интеграции. Эти *привязки*, которые представляют как входные, так и выходные данные, объявляются в определении функции. Данные привязок предоставляются функции в качестве параметров. Триггер является специальным типом входных привязок. В то время как функция обладает только одним триггером, она может состоять из нескольких входных и выходных привязок. Дополнительные сведения см. в статье [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md).

В этой статье приведены сведения об интеграции функции, которая была создана в [предыдущей статье](functions-create-first-function-python.md), с помощью очереди службы хранилища Azure. Выходная привязка, которая была добавлена в эту функцию, записывает в сообщение очереди данные HTTP-запроса. 

Большинство привязок требуют сохраненную строку подключения, которая будет использоваться Функциями Azure для доступа к привязанным службам. Чтобы упростить процесс, вам следует использовать учетную запись хранения, созданную в приложении-функции. Подключение к этой учетной записи уже хранится в параметрах приложения под названием `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с этой статьей следует завершить все шаги из статьи [Создание функции, активируемой HTTP, в Azure](functions-create-first-function-python.md).

## <a name="download-the-function-app-settings"></a>Загрузка параметров приложения-функции

В предыдущей статье вы создавали приложение-функцию и учетную запись хранения в Azure. Строка подключения данной учетной записи надежно хранится в параметрах приложения в Azure. В этой статье вы напишете сообщения в очередь хранилища в пределах одной учетной записи. Чтобы подключить учетную запись хранения при локальном выполнение функции, необходимо загрузить параметры приложения в файл local.settings.json. Чтобы загрузить параметры в local.settings.json, выполните команду Azure Functions Core Tools и замените `<APP_NAME>` именем приложения-функции из предыдущей статьи.

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Возможно, вам потребуется войти в учетную запись Azure.

> [!IMPORTANT]  
> Из-за того, что его содержимое является секретом, файл local.settings.json никогда не будет опубликован, и он должен быть исключен из системы управления версиями.

Вам нужно значение `AzureWebJobsStorage`, которое является строкой подключения к учетной записи хранилища. Это подключение используется для проверки работы привязки для вывода.

## <a name="add-an-output-binding"></a>Добавление выходной привязки

В службе "Функции" для каждого типа привязок требуется `direction`, `type` и уникальное `name`, которое определяется в файле function.json. В зависимости от типа привязки могут потребоваться дополнительные свойства. [Параметр выходных данных очереди](functions-bindings-storage-queue.md#output---configuration) описывает поля, требуемые для привязки очереди службы хранилища Azure.

Чтобы создать привязку, в файл `function.json` необходимо добавить объект настройки привязки. Чтобы добавить объект в массив `bindings`, который обладает свойствами, приведенными далее, выполните правку файла function.json в папке HttpTrigger.

| Свойство | Значение | ОПИСАНИЕ |
| -------- | ----- | ----------- |
| **`name`** | `msg` | Имя, которое используется для идентификации параметров привязки, указанных в коде. |
| **`type`** | `queue` | Привязка является привязкой очереди службы хранилища Azure. |
| **`direction`** | `out` | Привязка является выходной привязкой. |
| **`queueName`** | `outqueue` | Имя очереди, в которой записывается привязка. Если *queueName* не существует, то при первом использовании этот параметр будет создан привязкой. |
| **`connection`** | `AzureWebJobsStorage` | Имя параметра приложения, который содержит строку подключения учетной записи хранения. Параметр `AzureWebJobsStorage` содержит строку подключения учетной записи хранения, созданной в приложении-функции. |

Теперь файл function.json должен выглядеть следующим образом.

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

## <a name="add-code-that-uses-the-output-binding"></a>Добавление кода, который использует выходную привязку

После его настройки для доступа привязки к атрибуту Method в сигнатуре функции можно использовать ее `name`. В следующем примере `msg` является экземпляром [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest).

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

После использования выходной привязки вам для проверки подлинности, получения ссылки на очередь или записи данных больше не потребуется код пакета SDK службы хранилища Azure. Вместо вас эти задачи будут выполнены выходной привязкой очереди и средой выполнения функции.

## <a name="run-the-function-locally"></a>Локальное выполнение функции

Как и прежде, чтобы локально запустить среду выполнения функции, используйте следующую команду.

```bash
func host start
```

> [!NOTE]  
> Из-за того, что в предыдущей статье в файле host.json вы включили пакеты расширений, во время запуска для вас были загружены и установлены [расширения привязки службы хранилища](functions-bindings-storage-blob.md#packages---functions-2x).

Скопируйте URL-адрес функции `HttpTrigger` из выходных данных среды выполнения и вставьте его в адресную строку браузера. Добавьте строку запроса `?name=<yourname>` в этот URL-адрес и выполните запрос. В веб-браузере должен появиться ответ, который соответствует ответу из предыдущей статьи.

На этот раз в хранилище очередей выходная привязка также создает очередь с именем `outqueue` и с помощью этой же строки добавляет сообщение.

Затем Azure CLI следует использовать для просмотра новой очереди и проверки добавленного сообщения. Также очередь можно просматривать с помощью [Обозревателя службы хранилища Microsoft Azure][Azure Storage Explorer] или на [портале Azure](https://portal.azure.com).

### <a name="set-the-storage-account-connection"></a>Установка подключения к учетной записи хранилища

Откройте файл local.settings.json и скопируйте значение `AzureWebJobsStorage`, которое является строкой подключения к учетной записи хранилища. В качестве значения переменной среды `AZURE_STORAGE_CONNECTION_STRING` установите значение строки подключения, использовав следующую команду Bash.

```azurecli-interactive
export AZURE_STORAGE_CONNECTION_STRING=<STORAGE_CONNECTION_STRING>
```

После того, как значение строки подключения будет установлено вместо переменной среды `AZURE_STORAGE_CONNECTION_STRING`, вы можете получить доступ к учетной записи хранилища без необходимости проходить проверку подлинности каждый раз.

### <a name="query-the-storage-queue"></a>Запрос в очередь хранилища

Команду [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) можно использовать для просмотра очередей хранилища в учетной записи, как показано в следующем примере.

```azurecli-interactive
az storage queue list --output tsv
```

Выходные данные, полученные после выполнения этой команды, включают в себя очередь `outqueue`, содранную при выполнении функции.

Затем для просмотра сообщения в очереди используйте команду [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek), как показано в следующем примере.

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

Возвращаемая строка должна совпадать с сообщением, которое было отправлено для проверки функции.

> [!NOTE]  
> В предыдущем примере приведена декодированная строка, возвращенная из base64. Это происходит потому, что запись и чтение привязок хранилища очередей выполняется в хранилище Azure в качестве [строк base64](functions-bindings-storage-queue.md#encoding).

Теперь пора выполнить повторную публикацию обновленного приложения-функции в Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

Чтобы проверить развернутую функцию, вы можете снова использовать cURL в веб-браузере. Как и раньше, добавьте строку запроса `&name=<yourname>` к URL-адресу, как показано в следующем примере.

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Чтобы убедиться, что выходная привязка снова создает новое сообщение в очереди, вы можете [проверить сообщение очереди хранилища](#query-the-storage-queue).

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

Вы обновили функцию, активируемую HTTP, которую теперь можно использовать для записи данных в очередь хранилища. Чтобы получить дополнительные сведения об разработке Функций Azure с помощью Python, см. статьи [Справочник разработчика Python. Функции Azure](functions-reference-python.md) и [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md).

Затем для мониторинга приложения-функции следует включить Application Insights.

> [!div class="nextstepaction"]
> [Включение интеграции с Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/