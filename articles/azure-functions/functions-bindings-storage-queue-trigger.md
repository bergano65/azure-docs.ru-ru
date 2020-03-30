---
title: Триггер хранения очереди Azure для функций Azure
description: Научитесь выполнять функцию Azure как изменения данных хранения данных очереди Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 74ca984232bef979062221a451d0ee10a6965bc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277379"
---
# <a name="azure-queue-storage-trigger-for-azure-functions"></a>Триггер хранения очереди Azure для функций Azure

Триггер хранения очереди выполняет функцию по мере добавления сообщений в хранилище Azure Queue.

## <a name="encoding"></a>Кодирование

Функции ожидают строку в кодировке *base64*. Любая корректировка типа кодирования (для подготовки данных в виде строки в кодировке *base64*) должна быть реализована в вызывающей службе.

## <a name="example"></a>Пример

Используйте триггер очереди для запуска функции при получении нового элемента очереди. Сообщение очереди предоставляется в качестве входных данных функции.

# <a name="c"></a>[C #](#tab/csharp)

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

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

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

В разделе [Конфигурация](#configuration) описываются эти свойства.

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

В этом [разделе](#usage) показано свойство `myQueueItem`, имя которому назначает свойство `name` в function.json.  В разделе [Метаданные сообщений](#message-metadata) показаны все остальные переменные.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

В разделе [Конфигурация](#configuration) описываются эти свойства.

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

В этом [разделе](#usage) показано свойство `myQueueItem`, имя которому назначает свойство `name` в function.json.  В разделе [Метаданные сообщений](#message-metadata) показаны все остальные переменные.

# <a name="python"></a>[Python](#tab/python)

В следующем примере показано, как прочитать сообщение очереди, передаваемые функции через триггер.

Триггер очереди хранения определяется в *function.json,* где *тип* установлен на `queueTrigger`.

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

Код `func.ServiceBusMessage`init *\_.py объявляет параметр как, который позволяет читать сообщение очереди в вашей функции. _ \__*

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

# <a name="java"></a>[Java](#tab/java)

Следующий пример Java показывает функцию триггера очереди хранения, `myqueuename`которая регистрирует срабатывающее сообщение, помещенное в очередь.

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

 ---

## <a name="attributes-and-annotations"></a>Атрибуты и аннотации

# <a name="c"></a>[C #](#tab/csharp)

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте следующие атрибуты для настройки триггера очереди:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

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

  Можно установить `Connection` свойство для указания настройки приложения, содержащей строку подключения учетной записи хранилища для использования, как показано в следующем примере:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Для полного примера [см.](#example)

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

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

Атрибуты не поддерживаются скриптом C'.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Атрибуты не поддерживаются JavaScript.

# <a name="python"></a>[Python](#tab/python)

Атрибуты не поддерживаются Python.

# <a name="java"></a>[Java](#tab/java)

Аннотация `QueueTrigger` дает вам доступ к очереди, которая запускает функцию. Следующий пример делает сообщение очереди доступным `message` для функции через параметр.

```java
package com.function;
import com.microsoft.azure.functions.annotation.*;
import java.util.Queue;
import com.microsoft.azure.functions.*;

public class QueueTriggerDemo {
    @FunctionName("QueueTriggerDemo")
    public void run(
        @QueueTrigger(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") String message,
        final ExecutionContext context
    ) {
        context.getLogger().info("Queue message: " + message);
    }
}
```

| Свойство    | Описание |
|-------------|-----------------------------|
|`name`       | Объявляет имя параметра в подписи функции. При срабатывании функции значение этого параметра имеет содержимое сообщения очереди. |
|`queueName`  | Объявляет имя очереди в учетной записи хранилища. |
|`connection` | Указывает на строку подключения учетной записи хранилища. |

---

## <a name="configuration"></a>Параметр Configuration

В следующей таблице объясняется свойства связывающей конфигурации, `QueueTrigger` установленные в файле *function.json* и атрибуте.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**тип** | Недоступно| Нужно задать значение `queueTrigger`. Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction**| Недоступно | Только в файле *function.json*. Нужно задать значение `in`. Это свойство задается автоматически при создании триггера на портале Azure. |
|**name** | Недоступно |Имя переменной, содержащей полезные данные элемента очереди в коде функции.  |
|**очередьИмя** | **ОчередьИмя**| Имя очереди для опроса. |
|**Подключения** | **Подключения** |Имя параметра приложения, содержащего строку подключения к службе хранилища, используемой для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если `connection` вы установите "MyStorage", время выполнения функций ищет настройки приложения, которые называются "MyStorage". Если оставить строку `connection` пустой, среда выполнения службы "Функции" будет использовать строку подключения к службе хранилища по умолчанию для параметра приложения с именем `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Использование

# <a name="c"></a>[C #](#tab/csharp)

Доступ к данным сообщения с помощью параметра `string paramName`метода, например. Вы можете выполнить привязку к одному из следующих типов:

* Объект. Среда выполнения службы "Функции" десериализует полезные данные JSON в экземпляр произвольного класса, определенного в коде. 
* `string`
* `byte[]`
* [ОблакоКютюсообщения]

Если при попытке привязать к `CloudQueueMessage` вы получаете сообщение об ошибке, убедитесь, что у вас есть ссылка на [правильную версию пакета SDK для службы хранилища](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

Доступ к данным сообщения с помощью параметра `string paramName`метода, например. Значение, `paramName` указанное в `name` свойстве *function.json*. Вы можете выполнить привязку к одному из следующих типов:

* Объект. Среда выполнения службы "Функции" десериализует полезные данные JSON в экземпляр произвольного класса, определенного в коде. 
* `string`
* `byte[]`
* [ОблакоКютюсообщения]

Если при попытке привязать к `CloudQueueMessage` вы получаете сообщение об ошибке, убедитесь, что у вас есть ссылка на [правильную версию пакета SDK для службы хранилища](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[Javascript](#tab/javascript)

Полезная нагрузка элемента `context.bindings.<NAME>` `<NAME>` очереди доступна через место, где совпадает имя, определенное в *function.json.* Если полезная нагрузка Является JSON, значение десериализируется в объект.

# <a name="python"></a>[Python](#tab/python)

Доступ к сообщению очереди через параметр, набранный как [Очередь Message.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.queuemessage?view=azure-python)

# <a name="java"></a>[Java](#tab/java)

Аннотация [«КонтиТриггер»](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queuetrigger?view=azure-java-stable) дает вам доступ к сообщению очереди, которое вызвало функцию.

---

## <a name="message-metadata"></a>Метаданные сообщений

Триггер очереди предоставляет несколько [свойств метаданных](./functions-bindings-expressions-patterns.md#trigger-metadata). Эти свойства можно использовать как часть выражений привязки в других привязках или как параметры в коде. Свойства являются членами класса [Cloud'uemessage.](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage)

|Свойство|Тип|Описание|
|--------|----|-----------|
|`QueueTrigger`|`string`|Полезные данные очереди (если это допустимая строка). Если полезная нагрузка сообщения `QueueTrigger` очереди является строкой, имеет `name` такое же значение, как и переменная, названная свойством в *function.json.*|
|`DequeueCount`|`int`|Количество раз, когда сообщение было выведено из очереди.|
|`ExpirationTime`|`DateTimeOffset`|Время истечения срока действия сообщения.|
|`Id`|`string`|Идентификатор сообщения в очереди.|
|`InsertionTime`|`DateTimeOffset`|Время, когда сообщение было добавлено в очередь.|
|`NextVisibleTime`|`DateTimeOffset`|Время, когда сообщение станет видимым в следующий раз.|
|`PopReceipt`|`string`|Уведомление о получении сообщения.|

## <a name="poison-messages"></a>Ядовитые сообщения

При сбое функции триггера очереди по умолчанию служба "Функции Azure" выполняет ее для заданного сообщения очереди еще пять раз (включая первую попытку). Если все пять попыток не удаляют, время выполнения функций добавляет сообщение в очередь под названием * &lt;originalqueuename>-poison.* Можно написать функции для обработки сообщений из очереди подозрительных сообщений путем внесения их в журнал или отправки уведомления о необходимости ручного вмешательства.

Для обработки сообщений о сбоях вручную проверьте значение [dequeueCount](#message-metadata) сообщения очереди.

## <a name="polling-algorithm"></a>Алгоритм опроса

В триггере очереди реализован алгоритм случайной экспоненциальной отсрочки, который позволяет уменьшить влияние опроса очереди ожидающих задач на затраты на транзакции хранилища.

Алгоритм использует следующую логику:

- При обнаружении сообщения время выполнения ожидается две секунды, а затем проверяется на наличие другого сообщения
- Когда сообщение не найдено, оно ждет около четырех секунд, прежде чем попытаться снова.
- После последующих неудачных попыток получения сообщения очереди время ожидания продолжает увеличиваться, пока не достигнет максимального времени ожидания, по умолчанию — одна минута.
- Максимальное время ожидания настраивается при помощи свойства `maxPollingInterval` в [файле host.json](functions-host-json.md#queues).

Для локального развития максимальный интервал опроса по умолчанию составляет две секунды.

Что касается выставления счетов, то время, затрачиваемые на опрос по времени выполнения, является «бесплатным» и не учитывается в отношении вашей учетной записи.

## <a name="concurrency"></a>параллелизм

При наличии нескольких сообщений, ожидающих в очереди, триггер очереди извлекает пакет сообщений и в параллельном режиме вызывает экземпляры функций для обработки. По умолчанию в пакете содержится 16 сообщений. Когда число обрабатываемых сообщений снижается до 8, среда выполнения получает следующий пакет и начинает обработку содержащихся в нем сообщений. Поэтому максимальное количество сообщений, одновременно обрабатываемых каждой функцией на одной виртуальной машине, равно 24. Это ограничение применяется отдельно к каждой функции, активируемой с помощью очереди, на каждой виртуальной машине. Если приложение-функция масштабируется на несколько виртуальных машин, каждая машина будет ожидать триггеров и пытаться выполнить функции. Например, если приложение-функция масштабируется на 3 виртуальные машины, то максимальное количество параллельных экземпляров одной функции, активируемой с помощью очереди, равно 72.

Размер пакета и порог для получения нового пакета настраиваются в [файле host.json](functions-host-json.md#queues). Если в приложении-функции требуется свести к минимуму параллельное выполнение функций, активируемых с помощью очереди, вы можете задать для размера пакета значение 1. Этот параметр позволяет исключить параллелизм только при условии, что приложение-функция выполняется на одной виртуальной машине. 

Триггер очереди автоматически предотвращает многократную обработку функцией сообщения из очереди. Не обязательно создавать идемпотентные функции.

## <a name="hostjson-properties"></a>свойства host.json

В файле [host.json](functions-host-json.md#queues) содержатся параметры, управляющие поведением очереди триггера. Подробную информацию о доступных настройках можно найти в разделе [настройки host.json.](functions-bindings-storage-queue-output.md#hostjson-settings)

## <a name="next-steps"></a>Дальнейшие действия

- [Запись сообщений хранения очереди (связка выхода)](./functions-bindings-storage-blob-output.md)

<!-- LINKS -->

[ОблакоКютюсообщения]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
