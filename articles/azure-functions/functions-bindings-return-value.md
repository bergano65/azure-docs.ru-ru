---
title: Использование возвращаемого значения функции Azure
description: Сведения об управлении возвращаемые значения для функций Azure
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
origin.date: 01/14/2019
ms.date: 03/04/2019
ms.author: v-junlch
ms.openlocfilehash: 4ccfe192eaea94cb9b199bd3c6f0bdacf1685519
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437724"
---
# <a name="using-the-azure-function-return-value"></a>Использование возвращаемого значения функции Azure

В этой статье объясняется, как возвращаемые значения работы внутри функции.

В языках, которые должны иметь возвращаемого значения, можно привязать функцию [выходная привязка](./functions-triggers-bindings.md#binding-direction) к возвращаемому значению:

- В библиотеке классов C# примените атрибут выходной привязки к возвращаемому значению метода.
- В других языках задайте для свойства `name` значение `$return` в файле *function.json*.

При наличии нескольких выходных привязок используйте возвращаемое значение только для одной из них.

В C# и скрипте C# есть альтернативные способы отправки данных в привязку для вывода — с помощью параметров `out` и [объектов сборщика](functions-reference-csharp.md#writing-multiple-output-values).

Ознакомьтесь с примером использования возвращаемого значения для требуемого языка:

- [C#](#c-example)
- [Скрипт C# (CSX)](#c-script-example)
- [F#](#f-example)
- [JavaScript](#javascript-example)
- [Python](#python-example)

## <a name="c-example"></a>Пример C#

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

## <a name="c-script-example"></a>Пример сценария C#

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

## <a name="f-example"></a>Пример F#

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

## <a name="javascript-example"></a>Пример JavaScript

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

## <a name="python-example"></a>Пример на Python

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

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Обработка ошибок привязки функций Azure](./functions-bindings-errors.md)

