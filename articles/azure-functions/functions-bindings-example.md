---
title: Azure Functions trigger and binding example
description: Learn to configure Azure Function bindings
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 8685c0fe02ad6c68918736e857c2015e2bfb4595
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227239"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Azure Functions trigger and binding example

This article demonstrates how to configure a [trigger and bindings](./functions-triggers-bindings.md) in an Azure Function.

Предположим, вам нужно, чтобы каждый раз, когда в хранилище очередей Azure появляется новое сообщение, создавалась строка в хранилище таблиц Azure. Этот сценарий можно реализовать с помощью триггера для хранилища очередей Azure и выходной привязки хранилища таблиц Azure. 

Ниже представлен пример файла *function.json* для этого сценария. 

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "direction": "in",
      "name": "order",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "direction": "out",
      "name": "$return",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Первым элементом массива `bindings` является триггер хранилища очередей. Свойства `type` и `direction` идентифицируют триггер. Свойство `name` идентифицирует параметр функции, который будет получать содержимое из сообщения очереди. Отслеживаемая очередь имеет имя `queueName`, а строка подключения содержится в параметре приложения, указанном в `connection`.

Вторым элементом массива `bindings` является выходная привязка хранилища таблиц Azure. Свойства `type` и `direction` идентифицируют привязку. Свойство `name` указывает, как функция предоставит новую строку таблицы. В нашем примере используется возвращаемое значение функции. Таблица имеет имя `tableName`, а строка подключения содержится в параметре приложения, указанном в `connection`.

Чтобы просмотреть и отредактировать содержимое файла *function.json* на портале Azure, щелкните параметр **Расширенный редактор** на вкладке **Интегрировать** этой функции.

> [!NOTE]
> Параметр `connection` содержит имя параметра приложения, в котором хранится строка подключения, но не саму строку. Привязки используют параметры приложения для хранения строк подключения, чтобы обеспечить соблюдение рекомендаций: файл *function.json* не должен содержать секреты службы.

## <a name="c-script-example"></a>Пример сценария C#

Ниже приведен код скрипта C#, который работает с этим триггером и этой привязкой. Обратите внимание, что содержимое сообщения очереди предоставляется в параметре `order`. Это имя является обязательным, поскольку свойство `name` в файле *function.json* имеет значение `order`. 

```cs
#r "Newtonsoft.Json"

using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, ILogger log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

## <a name="javascript-example"></a>Пример JavaScript

The same *function.json* file can be used with a JavaScript function:

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

## <a name="class-library-example"></a>Class library example

В библиотеке классов эта информация о триггере и привязке &mdash; имена очередей и таблиц, учетные записи хранения, входные и выходные параметры функции &mdash; передаются в виде атрибутов вместо файла function.json. Ниже приведен пример:

```csharp
public static class QueueTriggerTableOutput
{
    [FunctionName("QueueTriggerTableOutput")]
    [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
    public static Person Run(
        [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order,
        ILogger log)
    {
        return new Person() {
                PartitionKey = "Orders",
                RowKey = Guid.NewGuid().ToString(),
                Name = order["Name"].ToString(),
                MobileNumber = order["MobileNumber"].ToString() };
    }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

You now have a working function that is triggered by an Azure Queue and outputs data to Azure Table storage.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Azure Functions binding expression patterns](./functions-bindings-expressions-patterns.md)
