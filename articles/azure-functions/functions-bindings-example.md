---
title: Пример триггера и привязки для Azure функции
description: Узнайте, как настроить привязки функций Azure
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
origin.date: 02/18/2019
ms.date: 03/04/2019
ms.author: v-junlch
ms.openlocfilehash: 6d5f9b171a4efc5e52d281655de143ac9d40d437
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437894"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Пример триггера и привязки для Azure функции

В этой статье показано, как настроить [триггера и привязки](./functions-triggers-bindings.md) в функции Azure.

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

Же *function.json* файл может использоваться и в функции JavaScript:

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

## <a name="class-library-example"></a>Пример библиотеки класса

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

Теперь у вас есть рабочий функции, активируемой хранилищем таблиц Azure, который выводит данные в очередь.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Шаблоны выражений привязки в функциях Azure](./functions-bindings-expressions-patterns.md)

