---
title: Using return value from an Azure Function
description: Learn to manage return values for Azure Functions
author: craigshoemaker
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 30c9caf267482d7c3731d4848cfb26cc8120b1ac
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231068"
---
# <a name="using-the-azure-function-return-value"></a>Using the Azure Function return value

This article explains how return values work inside a function.

In languages that have a return value, you can bind a function [output binding](./functions-triggers-bindings.md#binding-direction) to the return value:

* В библиотеке классов C# примените атрибут выходной привязки к возвращаемому значению метода.
* В других языках задайте для свойства `name` значение `$return` в файле *function.json*.

При наличии нескольких выходных привязок используйте возвращаемое значение только для одной из них.

В C# и скрипте C# есть альтернативные способы отправки данных в привязку для вывода — с помощью параметров `out` и [объектов сборщика](functions-reference-csharp.md#writing-multiple-output-values).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Это код C#, который использует возвращаемое значение для выходной привязки, за которым следует пример асинхронной функции:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="c-scripttabcsharp-script"></a>[C# Script](#tab/csharp-script)

Выходная привязка в файле *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Ниже приведен код скрипта C#, за которым следует пример асинхронной функции:

```cs
public static string Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="ftabfsharp"></a>[F#](#tab/fsharp)

Выходная привязка в файле *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Ниже показан код F#.

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Выходная привязка в файле *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

В JavaScript возвращаемое значение передается в качестве второго параметра в `context.done`:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Выходная привязка в файле *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Ниже приведен код Python.

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

---

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Handle Azure Functions binding errors](./functions-bindings-errors.md)
