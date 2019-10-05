---
title: Привязки хранилища очередей Azure для службы "Функции Azure"
description: Узнайте, как использовать триггер службы хранилища очередей Azure и выходную привязку в службе "Функции Azure".
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: функции azure, функции, обработка событий, динамические вычисления, независимая архитектура
ms.service: azure-functions
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: ff0490a7854d0398df925fc56f766470ca9d1618
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973452"
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Привязки хранилища очередей Azure для службы "Функции Azure"

В этой статье рассматривается работа с привязками хранилища очередей Azure в службе "Функции Azure". Служба "Функции Azure" поддерживает привязки триггера и выходные привязки для очередей.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Пакеты – Функции 1.x

Привязки служебной шины доступны в пакете NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) версии 2.х. Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Queue).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Пакеты — Функции 2.x

Привязки хранилища очередей доступны в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) версии 3.х. Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="encoding"></a>Кодировка
Функции ожидают строку в кодировке *base64*. Любая корректировка типа кодирования (для подготовки данных в виде строки в кодировке *base64*) должна быть реализована в вызывающей службе.

## <a name="trigger"></a>Триггер

Используйте триггер очереди для запуска функции при получении нового элемента очереди. Сообщение очереди предоставляется в качестве входных данных функции.

## <a name="trigger---example"></a>Пример триггера

Языковой пример см. в разделах:

* [C#](#trigger---c-example)
* [Скрипт C# (CSX)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Пример C# в триггере

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая выполняет опрос очереди `myqueue-items`, а затем делает запись в журнал при каждой обработке элемента очереди.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
}
```

### <a name="trigger---c-script-example"></a>Пример скрипта C# в триггере

В следующем примере показана привязка триггера очереди в файле *function.json* и коде [сценария C# (CSX)](functions-reference-csharp.md), который использует привязку. Эта функция выполняет опрос очереди `myqueue-items`, а затем делает запись в журнал при каждой обработке элемента очереди.

Ниже показан файл *function.json*.

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

В разделе [Конфигурация](#trigger---configuration) описываются эти свойства.

Ниже приведен код скрипта C#.

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

В этом [разделе](#trigger---usage) показано свойство `myQueueItem`, имя которому назначает свойство `name` в function.json.  В разделе [Метаданные сообщений](#trigger---message-metadata) показаны все остальные переменные.

### <a name="trigger---javascript-example"></a>Пример JavaScript в триггере

В следующем примере показана привязка триггера очереди в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует привязку. Эта функция выполняет опрос очереди `myqueue-items`, а затем делает запись в журнал при каждой обработке элемента очереди.

Ниже показан файл *function.json*.

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

В разделе [Конфигурация](#trigger---configuration) описываются эти свойства.

> [!NOTE]
> Параметр name отображается как `context.bindings.<name>` в коде JavaScript, который содержит полезные данные элемента очереди. Эти полезные данные также передаются в качестве второго параметра в функцию.

Ниже показан код JavaScript.

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

В этом [разделе](#trigger---usage) показано свойство `myQueueItem`, имя которому назначает свойство `name` в function.json.  В разделе [Метаданные сообщений](#trigger---message-metadata) показаны все остальные переменные.

### <a name="trigger---java-example"></a>Пример Java в триггере

В следующем примере Java показано триггерные функции очереди хранилища, которые регистрируют активированное сообщение, помещенное в очередь `myqueuename`.

 ```java
 @FunctionName("queueprocessor")
 public void run(
    @QueueTrigger(name = "msg",
                   queueName = "myqueuename",
                   connection = "myconnvarname") String message,
     final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
 ```

### <a name="trigger---python-example"></a>Пример Python: триггер

В следующем примере показано, как прочитать сообщение очереди, переданное в функцию через триггер.

Триггер очереди хранилища определен в *Function. JSON* , где *type* имеет значение `queueTrigger`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Код  *_\_init_\_.py* объявляет параметр как `func.ServiceBusMessage`, что позволяет считывать сообщение очереди в функции.

```python
import logging
import json

import azure.functions as func

def main(msg: func.QueueMessage):
    logging.info('Python queue trigger function processed a queue item.')

    result = json.dumps({
        'id': msg.id,
        'body': msg.get_body().decode('utf-8'),
        'expiration_time': (msg.expiration_time.isoformat()
                            if msg.expiration_time else None),
        'insertion_time': (msg.insertion_time.isoformat()
                           if msg.insertion_time else None),
        'time_next_visible': (msg.time_next_visible.isoformat()
                              if msg.time_next_visible else None),
        'pop_receipt': msg.pop_receipt,
        'dequeue_count': msg.dequeue_count
    })

    logging.info(result)
```

## <a name="trigger---attributes"></a>Атрибуты триггера

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте следующие атрибуты для настройки триггера очереди:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueTriggerAttribute.cs)

  Конструктор атрибута принимает имя очереди для отслеживания, как показано в следующем примере:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  Чтобы указать учетную запись хранения, которую нужно использовать, можно задать свойство `Connection`, как показано в следующем примере.

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Полный пример см. в разделе [Пример C# в триггере](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Предоставляет еще один способ указать используемую учетную запись хранения. Конструктор принимает имя параметра приложения, содержащего строку подключения к службе хранилища. Атрибут может применяться на уровне класса, метода или параметра. Ниже показан пример уровня класса и метода.

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Используемая учетная запись хранения определяется в следующем порядке:

* Свойство `QueueTrigger` атрибута `Connection`.
* Атрибут `StorageAccount`, примененный к тому же параметру, что и `QueueTrigger`.
* Атрибут `StorageAccount`, примененный к функции.
* Атрибут `StorageAccount`, примененный к классу.
* Параметр приложения AzureWebJobsStorage.

## <a name="trigger---configuration"></a>Конфигурация триггера

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `QueueTrigger`.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type** | Недоступно| Нужно задать значение `queueTrigger`. Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction**| Недоступно | Только в файле *function.json*. Нужно задать значение `in`. Это свойство задается автоматически при создании триггера на портале Azure. |
|**name** | Недоступно |Имя переменной, содержащей полезные данные элемента очереди в коде функции.  |
|**queueName** | **QueueName**| Имя очереди для опроса. |
|**подключение** | **Connection** |Имя параметра приложения, содержащего строку подключения к службе хранилища, используемой для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если задать для `connection` значение MyStorage, среда выполнения службы "Функции" будет искать параметр приложения с именем AzureWebJobsMyStorage. Если оставить строку `connection` пустой, среда выполнения службы "Функции" будет использовать строку подключения к службе хранилища по умолчанию для параметра приложения с именем `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Использование триггера

В коде и сценарии C# для доступа к данным сообщения используйте параметр метода, например `string paramName`. В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. Вы можете выполнить привязку к одному из следующих типов:

* Объект. Среда выполнения службы "Функции" десериализует полезные данные JSON в экземпляр произвольного класса, определенного в коде. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Если при попытке привязать к `CloudQueueMessage` вы получаете сообщение об ошибке, убедитесь, что у вас есть ссылка на [правильную версию пакета SDK для службы хранилища](#azure-storage-sdk-version-in-functions-1x).

В JavaScript используйте `context.bindings.<name>`, чтобы получить доступ к полезным данным элемента очереди. Если полезные данные представлены в виде JSON, они десериализируются в объект.

## <a name="trigger---message-metadata"></a>Метаданные сообщения триггера

Триггер очереди предоставляет несколько [свойств метаданных](./functions-bindings-expressions-patterns.md#trigger-metadata). Эти свойства можно использовать как часть выражений привязки в других привязках или как параметры в коде. Эти свойства относятся к классу [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage).

|Свойство|type|Описание|
|--------|----|-----------|
|`QueueTrigger`|`string`|Полезные данные очереди (если это допустимая строка). Если полезные данные очереди сообщений представлены в виде строки, значение `QueueTrigger` совпадает со значением переменной, имя которой назначено свойством `name` в файле *function.json*.|
|`DequeueCount`|`int`|Количество раз, когда сообщение было выведено из очереди.|
|`ExpirationTime`|`DateTimeOffset`|Время истечения срока действия сообщения.|
|`Id`|`string`|Идентификатор сообщения в очереди.|
|`InsertionTime`|`DateTimeOffset`|Время, когда сообщение было добавлено в очередь.|
|`NextVisibleTime`|`DateTimeOffset`|Время, когда сообщение станет видимым в следующий раз.|
|`PopReceipt`|`string`|Уведомление о получении сообщения.|

## <a name="trigger---poison-messages"></a>Триггеры сообщений о сбое

При сбое функции триггера очереди по умолчанию служба "Функции Azure" выполняет ее для заданного сообщения очереди еще пять раз (включая первую попытку). В случае сбоя всех пяти попыток среда выполнения функций добавляет сообщение в очередь с именем *&lt;имя_первоначальной_очереди>-poison*. Можно написать функции для обработки сообщений из очереди подозрительных сообщений путем внесения их в журнал или отправки уведомления о необходимости ручного вмешательства.

Для обработки сообщений о сбоях вручную проверьте значение [dequeueCount](#trigger---message-metadata) сообщения очереди.

## <a name="trigger---polling-algorithm"></a>Триггер алгоритма опроса

В триггере очереди реализован алгоритм случайной экспоненциальной отсрочки, который позволяет уменьшить влияние опроса очереди ожидающих задач на затраты на транзакции хранилища.  При обнаружении сообщения среда выполнения ожидает в течение двух секунд, а затем проверяет, не поступило ли еще одно сообщение. Если сообщение не найдено, она ожидает около четырех секунд перед повторной попыткой. После последующих неудачных попыток получения сообщения очереди время ожидания продолжает увеличиваться, пока не достигнет максимального времени ожидания, по умолчанию — одна минута. Максимальное время ожидания настраивается при помощи свойства `maxPollingInterval` в [файле host.json](functions-host-json.md#queues).

## <a name="trigger---concurrency"></a>Триггер параллелизма

При наличии нескольких сообщений, ожидающих в очереди, триггер очереди извлекает пакет сообщений и в параллельном режиме вызывает экземпляры функций для обработки. По умолчанию в пакете содержится 16 сообщений. Когда число обрабатываемых сообщений снижается до 8, среда выполнения получает следующий пакет и начинает обработку содержащихся в нем сообщений. Поэтому максимальное количество сообщений, одновременно обрабатываемых каждой функцией на одной виртуальной машине, равно 24. Это ограничение применяется отдельно к каждой функции, активируемой с помощью очереди, на каждой виртуальной машине. Если приложение-функция масштабируется на несколько виртуальных машин, каждая машина будет ожидать триггеров и пытаться выполнить функции. Например, если приложение-функция масштабируется на 3 виртуальные машины, то максимальное количество параллельных экземпляров одной функции, активируемой с помощью очереди, равно 72.

Размер пакета и порог для получения нового пакета настраиваются в [файле host.json](functions-host-json.md#queues). Если в приложении-функции требуется свести к минимуму параллельное выполнение функций, активируемых с помощью очереди, вы можете задать для размера пакета значение 1. Этот параметр позволяет исключить параллелизм только при условии, что приложение-функция выполняется на одной виртуальной машине. 

Триггер очереди автоматически предотвращает многократную обработку функцией сообщения из очереди. Не обязательно создавать идемпотентные функции.

## <a name="trigger---hostjson-properties"></a>Свойства host.json в триггере

В файле [host.json](functions-host-json.md#queues) содержатся параметры, управляющие поведением очереди триггера. Дополнительные сведения о доступных параметрах см. в разделе [Параметры Host. JSON](#hostjson-settings) .

## <a name="output"></a>Output

Используйте выходную привязку хранилища очередей Azure, чтобы записать сообщения в очередь.

## <a name="output---example"></a>Пример выходных данных

Языковой пример см. в разделах:

* [C#](#output---c-example)
* [Скрипт C# (CSX)](#output---c-script-example)
* [JavaScript](#output---javascript-example)
* [Java](#output---java-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Пример выходных данных C#

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая создает сообщения очереди для каждого полученного HTTP-запроса.

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

### <a name="output---c-script-example"></a>Пример выходных данных скрипта C#

В следующем примере показана привязка триггера HTTP в файле *function.json* и коде [сценария C# (CSX)](functions-reference-csharp.md), который использует привязку. Функция создает элемент очереди с полезными данными объекта **CustomQueueMessage** для каждого полученного HTTP-запроса.

Ниже показан файл *function.json*.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

В разделе [Конфигурация](#output---configuration) описываются эти свойства.

Ниже приведен код скрипта C#, который создает одно сообщение очереди.

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

За один раз можно отправить несколько сообщений с помощью параметра `ICollector` или `IAsyncCollector`. Ниже приведен код скрипта C#, который отправляет несколько сообщений (одно — с данными HTTP-запроса, а другое — с кодовыми значениями).

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

### <a name="output---javascript-example"></a>Пример выходных данных JavaScript

В следующем примере показана привязка триггера HTTP в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует привязку. Эта функция создает элемент очереди для каждого полученного HTTP-запроса.

Ниже показан файл *function.json*.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

В разделе [Конфигурация](#output---configuration) описываются эти свойства.

Ниже показан код JavaScript.

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Вы можете отправить несколько сообщений одновременно, определив массив сообщений для выходной привязки `myQueueItem`. Следующий код JavaScript отправляет два сообщения очереди с четко фиксированными значениями для каждого полученного HTTP-запроса.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

### <a name="output---java-example"></a>Пример выходных данных Java

 В следующем примере показана функция Java, которая создает сообщения очереди, если оно активировано HTTP-запросом.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "AzureWebJobsStorage")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding&lt;String&gt; result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

В [библиотеке среды выполнения функций Java](/java/api/overview/azure/functions/runtime) используйте заметку `@QueueOutput` для параметров, значения которых будут записываться в хранилище очередей.  Параметр должен быть типа `OutputBinding<T>`, где T — любой собственный тип Java POJO.

### <a name="output---python-example"></a>Пример Python: выходные данные

В следующем примере показано, как вывести одно и несколько значений в очереди хранилища. Конфигурация, необходимая для *Function. JSON* , такая же, как и та же.

Привязка очереди хранилища определяется в *Function. JSON* , где *type* имеет значение `queue`.

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
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Чтобы задать отдельное сообщение в очереди, необходимо передать одно значение в метод `set`.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Чтобы создать несколько сообщений в очереди, объявите параметр в качестве соответствующего типа списка и передайте массив значений (который соответствует типу списка) в метод `set`.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

## <a name="output---attributes"></a>Выходные атрибуты

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs).

Этот атрибут применяется к параметру `out` или возвращаемому значению функции. Конструктор атрибута принимает имя очереди, как показано в следующем примере:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Чтобы указать учетную запись хранения, которую нужно использовать, можно задать свойство `Connection`, как показано в следующем примере.

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Полный пример см. в разделе [Пример выходных данных C#](#output---c-example).

Чтобы указать учетную запись хранения на уровне класса, метода или параметра, можно использовать атрибут `StorageAccount`. Дополнительные сведения см. в разделе "Атрибуты триггера".

## <a name="output---configuration"></a>Выходная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `Queue`.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type** | Недоступно | Нужно задать значение `queue`. Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction** | Недоступно | Нужно задать значение `out`. Это свойство задается автоматически при создании триггера на портале Azure. |
|**name** | Недоступно | Имя переменной, представляющей очередь в коде функции. Задайте значение `$return`, ссылающееся на возвращаемое значение функции.|
|**queueName** |**QueueName** | Имя очереди. |
|**подключение** | **Connection** |Имя параметра приложения, содержащего строку подключения к службе хранилища, используемой для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если задать для `connection` значение MyStorage, среда выполнения службы "Функции" будет искать параметр приложения с именем AzureWebJobsMyStorage. Если оставить строку `connection` пустой, среда выполнения службы "Функции" будет использовать строку подключения к службе хранилища по умолчанию для параметра приложения с именем `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Использование выходной привязки

В коде и скрипте C# запишите одно сообщение очереди с помощью параметра метода, например `out T paramName`. В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. Вы можете использовать этот метод типа возвращаемого значения вместо параметра `out`. `T` может быть любого из следующих типов:

* Объект, сериализуемый как JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Если при попытке привязать к `CloudQueueMessage` вы получаете сообщение об ошибке, убедитесь, что у вас есть ссылка на [правильную версию пакета SDK для службы хранилища](#azure-storage-sdk-version-in-functions-1x).

В коде и скрипте C# запишите несколько сообщений очереди с помощью одного из следующих типов: 

* `ICollector<T>` или `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue).

В функциях JavaScript используйте `context.bindings.<name>`, чтобы получить доступ к выходной очереди сообщений. Строку или сериализуемый объект JSON можно использовать для полезных данных элемента очереди.


## <a name="exceptions-and-return-codes"></a>Исключения и коды возврата

| Привязка |  Ссылка |
|---|---|
| Очередь | [Коды ошибок очередей](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Большой двоичный объект, таблица, очередь | [Коды ошибок хранилища](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Большой двоичный объект, таблица, очередь |  [Устранение неполадок](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Параметры файла host.json

В этом разделе описываются глобальные параметры конфигурации, доступные для этой привязки в версии 2.x. В приведенном ниже примере файла host.json содержатся только параметры версии 2.x для этой привязки. Дополнительные сведения о глобальных настройках конфигурации в версии 2.x смотрите в статье [Справочник по файлу host.json для Функций Azure](functions-host-json.md).

> [!NOTE]
> Чтобы получить дополнительные сведения о файле host.json в Функции 1.x, см. статью [host.json reference for Azure Functions 1.x](functions-host-json-v1.md)(Справочник по файлу host.json для службы "Функции Azure" версии 1.x.).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8
        }
    }
}
```


|Свойство  |Значение по умолчанию | Описание |
|---------|---------|---------|
|maxPollingInterval|00:00:01|Максимальный интервал между опросами очереди. Минимальное значение — 00:00:00.100 (100 мс) и увеличивается до 00:01:00 (1 мин.).  В 1. x тип данных равен миллисекундам, а в 2. x — интервал времени.|
|visibilityTimeout|00:00:00|Интервал времени между повторными попытками, когда при обработке сообщения возникает сбой. |
|batchSize|16|Количество сообщений очереди, которые среда выполнения функций одновременно получает и обрабатывает в параллельном режиме. Когда число обрабатываемых сообщений достигает `newBatchThreshold`, среда выполнения получает следующий пакет и начинает обработку содержащихся в нем сообщений. Поэтому максимальное количество сообщений, одновременно обрабатываемых каждой функцией, равно `batchSize` плюс `newBatchThreshold`. Это ограничение применяется отдельно к каждой функции, активируемой с помощью очереди. <br><br>Если вы не хотите, чтобы сообщения из одной очереди обрабатывались параллельно, можно установить для `batchSize` значение 1. Тем не менее этот параметр позволяет исключить параллелизм только при условии, что приложение-функция выполняется на одной виртуальной машине. Если приложение-функция развернуто на нескольких виртуальных машинах, каждая машина может запускать один экземпляр каждой функции, активируемой с помощью очереди.<br><br>Максимальное значение `batchSize` — 32. |
|maxDequeueCount|5|Число повторных попыток обработки сообщения, прежде чем поместить его в очередь подозрительных сообщений.|
|newBatchThreshold|batchSize/2|Каждый раз, когда количество сообщений, обрабатываемых параллельно, достигает этого числа, среда выполнения получает другой пакет.|

## <a name="next-steps"></a>Следующие шаги

* [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)

<!--
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Queue storage trigger](functions-create-storage-queue-triggered-function.md)
-->

> [!div class="nextstepaction"]
> [Добавление сообщений в очередь службы хранилища Azure с помощью Функций](functions-integrate-storage-queue-output-binding.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
