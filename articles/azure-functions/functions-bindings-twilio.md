---
title: Привязка Twilio для службы "Функции Azure"
description: Узнайте, как использовать привязки Twilio с функциями Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 07/09/2018
ms.author: cshoe
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ee78234b43e94a5c537161556410ee6246e625ca
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230948"
---
# <a name="twilio-binding-for-azure-functions"></a>Привязка Twilio для службы "Функции Azure"

В этой статье описано, как отправлять текстовые сообщения с помощью привязок [Twilio](https://www.twilio.com/) в службу "Функции Azure". Служба "Функции Azure" поддерживают выходные привязки для Twilio.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Пакеты – Функции 1.x

Привязки Twilio доступны в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio), версия 1.х. Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.Twilio/).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Пакеты — Функции 2.x

Привязки Twilio доступны в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio), версия 3.х. Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example---functions-1x"></a>Пример — функции 1.x

Языковой пример см. в разделах:

* [C#](#c-example)
* [Сценарий C# (CSX)](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>Пример C#

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая отправляет текстовое сообщение при активации сообщением из очереди.

```cs
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static SMSMessage Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {order}");

    var message = new SMSMessage()
    {
        Body = $"Hello {order["name"]}, thanks for your order!",
        To = order["mobileNumber"].ToString()
    };

    return message;
}
```

В этом примере используется `TwilioSms` атрибут с помощью возвращаемого значения метода. Альтернативой является использование атрибута с параметрами `out SMSMessage`, `ICollector<SMSMessage>` или `IAsyncCollector<SMSMessage>`.

### <a name="c-script-example"></a>Пример сценария C#

В следующем примере показаны выходная привязка Twilio*function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. Эта функция использует параметр `out` для отправки текстового сообщения.

Данные привязки в файле *function.json*:

Пример файла function.json:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Ниже приведен код сценария C#:

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    message = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    message.Body = msg;
    message.To = order.mobileNumber;
}
```

Вы не можете использовать параметры вывода в асинхронном коде. Ниже приведен пример асинхронного кода сценария C#:

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Twilio;

public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    smsText.Body = msg;
    smsText.To = order.mobileNumber;

    await message.AddAsync(smsText);
}
```

### <a name="javascript-example"></a>Пример JavaScript

В следующем примере показана выходная привязка Twilio в файле *function.json* и функции [JavaScript](functions-reference-node.md), которая использует привязку.

Данные привязки в файле *function.json*:

Пример файла function.json:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Ниже показан код JavaScript.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="example---functions-2x"></a>Пример — функции 2.x

Языковой пример см. в разделах:

* [2.x C#](#2x-c-example)
* [Скрипт 2.x C# (CSX)](#2x-c-script-example)
* [2.x JavaScript](#2x-javascript-example)

### <a name="2x-c-example"></a>Пример 2.x C#

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая отправляет текстовое сообщение при активации сообщением из очереди.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;
namespace TwilioQueueOutput
{
    public static class QueueTwilio
    {
        [FunctionName("QueueTwilio")]
        [return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
        public static CreateMessageOptions Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
        ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed: {order}");

            var message = new CreateMessageOptions(new PhoneNumber(order["mobileNumber"].ToString()))
            {
                Body = $"Hello {order["name"]}, thanks for your order!"
            };

            return message;
        }
    }
}
```

В этом примере используется `TwilioSms` атрибут с помощью возвращаемого значения метода. Альтернативой является использование атрибута с параметрами `out CreateMessageOptions`, `ICollector<CreateMessageOptions>` или `IAsyncCollector<CreateMessageOptions>`.

### <a name="2x-c-script-example"></a>Пример сценария 2.x C#

В следующем примере показаны выходная привязка Twilio*function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. Эта функция использует параметр `out` для отправки текстового сообщения.

Данные привязки в файле *function.json*:

Пример файла function.json:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Ниже приведен код сценария C#:

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static void Run(string myQueueItem, out CreateMessageOptions message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    message = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    message.Body = msg;
}
```

Вы не можете использовать параметры вывода в асинхронном коде. Ниже приведен пример асинхронного кода сценария C#:

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static async Task Run(string myQueueItem, IAsyncCollector<CreateMessageOptions> message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    CreateMessageOptions smsText = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    smsText.Body = msg;

    await message.AddAsync(smsText);
}
```

### <a name="2x-javascript-example"></a>Пример 2.x JavaScript

В следующем примере показана выходная привязка Twilio в файле *function.json* и функции [JavaScript](functions-reference-node.md), которая использует привязку.

Данные привязки в файле *function.json*:

Пример файла function.json:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Ниже показан код JavaScript.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message in the binding, you must at least
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. The "To" number 
    // must be specified in code. 
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="attributes"></a>Атрибуты

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [TwilioSms](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs).

Дополнительные сведения о настройке свойств атрибутов см. в разделе [Конфигурация](#configuration). Ниже приведен пример атрибута `TwilioSms` в сигнатуре метода:

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
public static CreateMessageOptions Run(
[QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, ILogger log)
{
    ...
}
 ```

Полный пример см. в разделе [Пример C#](#c-example).

## <a name="configuration"></a>Настройка

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `TwilioSms`.

| Свойство function.json версии 1 | Свойство function.json версии 2 | Свойство атрибута |Описание|
|---------|---------|---------|----------------------|
|**type**|**type**| Нужно задать значение `twilioSms`.|
|**direction**|**direction**| Нужно задать значение `out`.|
|**name**|**name**| Имя переменной, используемое в коде функции для текстового SMS-сообщения Twilio. |
|**accountSid**|**accountSidSetting**| **AccountSidSetting**| Требуемое значение: имя параметра приложения, содержащего идентификатор безопасности учетной записи Twilio, например TwilioAccountSid. Если значение не задано, имя параметра приложения по умолчанию — AzureWebJobsTwilioAccountSid. |
|**authToken**|**authTokenSetting**|**AuthTokenSetting**| Требуемое значение: имя параметра приложения, содержащего токен проверки подлинности Twilio, например TwilioAccountAuthToken. Если значение не задано, имя параметра приложения по умолчанию — AzureWebJobsTwilioAuthToken. |
|**to**| Недоступно — указать в коде | **To**| Требуемое значение: номер телефона, на который отправляется текст SMS-сообщения.|
|**from**|**from** | **from**| Требуемое значение: номер телефона, с которого отправляется текст SMS-сообщения.|
|**body**|**body** | **Текст**| Это значение можно использовать для жесткого кодирования текстового SMS-сообщения, если его не нужно задавать динамически в коде функции. |  

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)
