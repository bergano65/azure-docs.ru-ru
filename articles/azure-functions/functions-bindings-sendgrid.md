---
title: Привязки SendGrid для Функций Azure
description: Справочник по привязкам SendGrid для службы "Функции Azure".
author: craigshoemaker
ms.topic: conceptual
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: a96cd537328a1a9edeeb03f81350ed5393806765
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927579"
---
# <a name="azure-functions-sendgrid-bindings"></a>Привязки SendGrid для Функций Azure

В этой статье объясняется, как отправить электронное сообщение с помощью привязок [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) в службе "Функции Azure". Служба "Функции Azure" поддерживают выходную привязку для SendGrid.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Пакеты – Функции 1.x

Привязки SendGrid доступны в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) версии 2.x. Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Packages — функции 2. x и более поздних версий

Привязки SendGrid доступны в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) версии 3.x. Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/).

> [!NOTE]
> Версии 2. x и выше не создают раздел или подписку, настроенные в экземпляре `ServiceBusTrigger`. Эти версии основаны на [Microsoft. Azure. servicebus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) , который не обрабатывает управление очередями.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Пример

Языковой пример см. в разделах:

* [C#](#c-example)
* [Сценарий C# (CSX)](#c-script-example)
* [JavaScript](#javascript-example)
* [Java](#java-example)

### <a name="c-example"></a>Пример C#

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая использует триггер очереди служебной шины и выходную привязку SendGrid.

#### <a name="synchronous-c-example"></a>Пример синхронного C#:

```cs
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

message = new SendGridMessage();
message.AddTo(emailObject.To);
message.AddContent("text/html", emailObject.Body);
message.SetFrom(new EmailAddress(emailObject.From));
message.SetSubject(emailObject.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```
#### <a name="asynchronous-c-example"></a>Пример асинхронного C#:

```cs
[FunctionName("SendEmail")]
public static async void Run(
 [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
 [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] IAsyncCollector<SendGridMessage> messageCollector)
{
 var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

 var message = new SendGridMessage();
 message.AddTo(emailObject.To);
 message.AddContent("text/html", emailObject.Body);
 message.SetFrom(new EmailAddress(emailObject.From));
 message.SetSubject(emailObject.Subject);
 
 await messageCollector.AddAsync(message);
}

public class OutgoingEmail
{
 public string To { get; set; }
 public string From { get; set; }
 public string Subject { get; set; }
 public string Body { get; set; }
}
```

Вы можете не задавать свойство атрибута `ApiKey` при наличии ключа API в настройка приложения с именем AzureWebJobsSendGridApiKey.

### <a name="c-script-example"></a>Пример сценария C#

В следующем примере показаны выходная привязка SendGrid в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку.

Данные привязки в файле *function.json*:

```json 
{
    "bindings": [
        {
          "type": "queueTrigger",
          "name": "mymsg",
          "queueName": "myqueue",
          "connection": "AzureWebJobsStorage",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "$return",
          "direction": "out",
          "apiKey": "SendGridAPIKeyAsAppSetting",
          "from": "{FromEmail}",
          "to": "{ToEmail}"
        }
    ]
}
```

В разделе [Конфигурация](#configuration) описываются эти свойства.

Ниже приведен код скрипта C#.

```csharp
#r "SendGrid"

using System;
using SendGrid.Helpers.Mail;
using Microsoft.Azure.WebJobs.Host;

public static SendGridMessage Run(Message mymsg, ILogger log)
{
    SendGridMessage message = new SendGridMessage()
    {
        Subject = $"{mymsg.Subject}"
    };
    
    message.AddContent("text/plain", $"{mymsg.Content}");

    return message;
}
public class Message
{
    public string ToEmail { get; set; }
    public string FromEmail { get; set; }
    public string Subject { get; set; }
    public string Content { get; set; }
}
```

### <a name="java-example"></a>Пример Java

В следующем примере используется заметка `@SendGridOutput` из [библиотеки среды выполнения функций Java](/java/api/overview/azure/functions/runtime) для отправки сообщения электронной почты с помощью выходной привязки SendGrid.

```java
@FunctionName("SendEmail")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @SendGridOutput(
                name = "email", dataType = "String", apiKey = "SendGridConnection", to = "test@example.com", from = "test@example.com",
                subject= "Sending with SendGrid", text = "Hello from Azure Functions"
                ) OutputBinding<String> email
            )
    {
        String name = request.getBody().orElse("World");

        final String emailBody = "{\"personalizations\":" +
                                    "[{\"to\":[{\"email\":\"test@example.com\"}]," +
                                    "\"subject\":\"Sending with SendGrid\"}]," +
                                    "\"from\":{\"email\":\"test@example.com\"}," +
                                    "\"content\":[{\"type\":\"text/plain\",\"value\": \"Hello" + name + "\"}]}";

        email.setValue(emailBody);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
    }
```

### <a name="javascript-example"></a>Пример JavaScript

В следующем примере показаны выходная привязка SendGrid в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует эту привязку.

Данные привязки в файле *function.json*:

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

В разделе [Конфигурация](#configuration) описываются эти свойства.

Ниже показан код JavaScript.

```javascript
module.exports = function (context, input) {    
    var message = {
         "personalizations": [ { "to": [ { "email": "sample@sample.com" } ] } ],
        from: { email: "sender@contoso.com" },        
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};
```

## <a name="attributes"></a>Атрибуты

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs).

Дополнительные сведения о настройке свойств атрибутов см. в разделе [Конфигурация](#configuration). Ниже приведен пример атрибута `SendGrid` в сигнатуре метода:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Полный пример см. в разделе [Пример C#](#c-example).

## <a name="configuration"></a>Настройка

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `SendGrid`.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type**|| Обязательное. Необходимо задать значение `sendGrid`.|
|**direction**|| Обязательное. Необходимо задать значение `out`.|
|**name**|| Обязательное. Имя переменной, из которой в коде функции можно получить запрос или текст запроса. Это значение равно ```$return``` при наличии только одного возвращаемого значения. |
|**apiKey**|**ApiKey**| Имя параметра приложения, в котором содержится ваш ключ API. Если значение не задано, имя параметра приложения по умолчанию AzureWebJobsSendGridApiKey.|
|**to**|**To**| Электронный адрес получателя. |
|**from**|**from**| Электронный адрес отправителя. |
|**subject**|**Тема**| Тема электронного сообщения. |
|**text**|**текст**| Содержимое электронного сообщения. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Параметры файла host.json

В этом разделе описаны глобальные параметры конфигурации, доступные для этой привязки в версиях 2. x и более поздних. Пример файла host. JSON ниже содержит только параметры версии 2. x + для этой привязки. Дополнительные сведения о глобальных параметрах конфигурации в версиях 2. x и более поздних версий см. в [справочнике по Host. JSON для функций Azure](functions-host-json.md).

> [!NOTE]
> Сведения о файле host.json в Функциях Azure версии 1.x см. в [этой статье](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "sendGrid": {
            "from": "Azure Functions <samples@functions.com>"
        }
    }
}
```  

|Свойство  |значение по умолчанию | Описание |
|---------|---------|---------| 
|from|Н/Д|Адрес электронной почты для всех функций.| 


## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)
