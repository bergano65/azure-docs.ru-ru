---
title: Привязка триггера службы SignalR для функций Azure
description: Узнайте, как отправить сообщения службы SignalR из функций Azure.
author: chenyl
ms.topic: reference
ms.date: 05/11/2020
ms.author: chenyl
ms.openlocfilehash: c2ad9b6c4410a62d5652050406e05be4cde5fab0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85830712"
---
# <a name="signalr-service-trigger-binding-for-azure-functions"></a>Привязка триггера службы SignalR для функций Azure

Используйте привязку триггера *SignalR* для реагирования на сообщения, отправленные из службы Azure SignalR. При запуске функции сообщения, передаваемые в функцию, анализируются как объект JSON.

Сведения об установке и настройке см. в [этой обзорной статье](functions-bindings-signalr-service.md).

## <a name="example"></a>Пример

В следующем примере показана функция, которая получает сообщение с помощью привязки триггера и регистрирует сообщение.

# <a name="c"></a>[C#](#tab/csharp)

Привязка триггера службы SignalR для C# имеет две модели программирования. Модель на основе класса и традиционная модель. Модель на основе классов может обеспечить единообразный интерфейс программирования на стороне сервера SignalR. Традиционная модель обеспечивает большую гибкость и схожую с другими привязками функций.

### <a name="with-class-based-model"></a>Модель на основе класса

Дополнительные сведения см. в разделе [модель на основе класса](../azure-signalr/signalr-concept-serverless-development-config.md#class-based-model) .

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("SignalRTest")]
    public async Task SendMessage([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
    }
}
```

### <a name="with-traditional-model"></a>Традиционная модель

Традиционная модель подчиняются соглашению о функции Azure, разработанной C#. Если вы не знакомы с ней, можно изучить [документы](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-class-library).

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage", parameterNames: new string[] {"message"})]InvocationContext invocationContext, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

#### <a name="use-attribute-signalrparameter-to-simplify-parameternames"></a>`[SignalRParameter]`Упростите использование атрибута`ParameterNames`

По мере того, как это удобно для использования `ParameterNames` , `SignalRParameter` предоставляется для достижения той же цели.

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage")]InvocationContext invocationContext, [SignalRParameter]string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

Данные привязки в файле *function.json*:

Пример файла function.json:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Вот код сценария C#:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using System;
using Microsoft.Azure.WebJobs.Extensions.SignalRService;
using Microsoft.Extensions.Logging;

public static void Run(InvocationContext invocation, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Данные привязки в файле *function.json*:

Пример файла function.json:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Ниже показан код JavaScript.

```javascript
module.exports = function (context, invocation) {
    context.log(`Receive ${context.bindingData.message} from ${invocation.ConnectionId}.`)
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Данные привязки в файле *function.json*:

Пример файла function.json:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Ниже приведен код Python.

```python
import logging
import json
import azure.functions as func

def main(invocation) -> None:
    invocation_json = json.loads(invocation)
    logging.info("Receive {0} from {1}".format(invocation_json['Arguments'][0], invocation_json['ConnectionId']))
```

---

## <a name="configuration"></a>Параметр Configuration

### <a name="signalrtrigger"></a>сигналртригжер

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `SignalRTrigger`.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type**| н/д | Нужно задать значение `SignalRTrigger`.|
|**direction**| н/д | Нужно задать значение `in`.|
|**name**| Недоступно | Имя переменной, используемое в коде функции для объекта контекста вызова триггера. |
|**hubName**|**HubName**| Этому значению должно быть присвоено имя концентратора SignalR, чтобы активировать функцию.|
|**category**|**Категория**| Это значение должно быть задано в качестве категории сообщений для активации функции. Категория может иметь одно из следующих значений: <ul><li>**подключения**: включение *подключенных* и *отключенных* событий</li><li>**сообщения**: включение всех других событий, кроме тех, которые находятся в категории *соединений*</li></ul> |
|**event**|**Событие**| Это значение должно быть задано как событие сообщений для запуска функции. Для категории *Messages* событие является *целевым объектом* в [сообщении вызова](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) , отправляемом клиентами. Для категории *соединений* используется только *соединение и подключение* *отключено* . |
|**параметернамес**|**параметернамес**| Используемых Список имен, привязанных к параметрам. |
|**коннектионстрингсеттинг**|**ConnectionStringSetting**| Имя параметра приложения, который содержит строку подключения службы SignalR (по умолчанию — AzureSignalRConnectionString) |

## <a name="payload"></a>Полезные данные

Тип входных данных триггера объявлен как `InvocationContext` или пользовательский тип. При выборе `InvocationContext` вы получите полный доступ к содержимому запроса. При объявлении пользовательского типа среда выполнения попытается проанализировать текст запроса JSON для задания свойств объекта.

### <a name="invocationcontext"></a>инвокатионконтекст

Инвокатионконтекст содержит все содержимое в сообщении Send из службы SignalR.

|Свойство в Инвокатионконтекст | Описание:|
|------------------------------|------------|
|Аргументы| Доступно для категории *сообщений* . Содержит *аргументы* в [сообщении о вызове](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)|
|Ошибка| Доступно для события *disconnected* . Оно может быть пустым, если соединение закрыто без ошибок или содержит сообщения об ошибках.|
|Microsoft Translator Hub| Имя концентратора, которому принадлежит сообщение.|
|Категория| Категория сообщения.|
|событие| Событие сообщения.|
|ConnectionId| Идентификатор соединения клиента, который отправляет сообщение.|
|UserId| Удостоверение пользователя клиента, который отправляет сообщение.|
|Заголовки| Заголовки запроса.|
|Запрос| Запрос запроса, когда клиенты подключаются к службе.|
|Утверждения| Утверждения клиента.|

## <a name="using-parameternames"></a>Использование `ParameterNames`

Свойство `ParameterNames` в `SignalRTrigger` позволяет привязывать аргументы сообщений вызова к параметрам функций. Это предоставляет более удобный способ доступа к аргументам `InvocationContext` .

Предположим, у вас есть клиент SignalR для JavaScript, пытающийся вызвать метод `broadcast` в функции Azure с двумя аргументами.

```javascript
await connection.invoke("broadcast", message1, message2);
```

Можно получить доступ к этим двум аргументам из параметра, а также назначить для них тип параметра с помощью `ParameterNames` .

### <a name="remarks"></a>Комментарии

Для привязки параметра порядок важен. Если используется `ParameterNames` , порядок в `ParameterNames` совпадает с порядком аргументов, которые вызываются в клиенте. Если вы используете атрибут `[SignalRParameter]` в C#, порядок аргументов в методах функции Azure соответствует порядку аргументов в клиентах.

`ParameterNames`атрибут и `[SignalRParameter]` **не может** использоваться одновременно, или вы получите исключение.

## <a name="send-messages-to-signalr-service-trigger-binding"></a>Отправка сообщений в привязку триггера службы SignalR

Функция Azure создает URL-адрес для привязки триггера службы SignalR и имеет следующий формат:

```http
https://<APP_NAME>.azurewebsites.net/runtime/webhooks/signalr?code=<API_KEY>
```

Создается `API_KEY` функцией Azure. Вы можете получить `API_KEY` из портал Azure, так как вы используете привязку триггера службы SignalR.
:::image type="content" source="media/functions-bindings-signalr-service/signalr-keys.png" alt-text="Ключ API":::

Этот URL-адрес необходимо задать в `UrlTemplate` параметрах вышестоящей службы SignalR.

## <a name="next-steps"></a>Дальнейшие шаги

* [Azure Functions development and configuration with Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md) (Разработка и настройка функций Azure с помощью Службы Azure SignalR)
* [Пример привязки триггера службы SignalR](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples/bidirectional-chat)