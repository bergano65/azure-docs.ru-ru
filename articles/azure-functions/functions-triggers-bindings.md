---
title: Триггеры и привязки в решении "Функции Azure"
description: Learn to use triggers and bindings to connect your Azure Function to online events and cloud-based services.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d41fd7f66ecef3a563345424d7dc4366e47d3f0e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226561"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Основные понятия триггеров и привязок в Функциях Azure

In this article you learn the high-level concepts surrounding functions triggers and bindings.

Triggers are what cause a function to run. A trigger defines how a function is invoked and a function must have exactly one trigger. Triggers have associated data, which is often provided as the payload of the function. 

Binding to a function is a way of declaratively connecting another resource to the function; bindings may be connected as *input bindings*, *output bindings*, or both. Данные привязок предоставляются функции в качестве параметров.

You can mix and match different bindings to suit your needs. Bindings are optional and a function might have one or multiple input and/or output bindings.

Triggers and bindings let you avoid hardcoding access to other services. Функция получает нужные данные (например, содержимое сообщения из очереди) в виде параметров. Вы отправляете данные (например, для создания в очереди сообщения) с использованием возвращаемого значения функции. 

Consider the following examples of how you could implement different functions.

| Пример сценария | Триггер | Input binding | Output binding |
|-------------|---------|---------------|----------------|
| A new queue message arrives which runs a function to write to another queue. | Queue<sup>*</sup> | *None* | Queue<sup>*</sup> |
|A scheduled job reads Blob Storage contents and creates a new Cosmos DB document. | Таймер | Хранилище BLOB-объектов | База данных Cosmos |
|The Event Grid is used to read an image from Blob Storage and a document from Cosmos DB to send an email. | Служба "Сетка событий Azure" | Blob Storage and  Cosmos DB | SendGrid |
| A webhook that uses Microsoft Graph to update an Excel sheet. | HTTP | *None* | Microsoft Graph |

<sup>\*</sup> Represents different queues

These examples are not meant to be exhaustive, but are provided to illustrate how you can use triggers and bindings together.

###  <a name="trigger-and-binding-definitions"></a>Trigger and binding definitions

Triggers and bindings are defined differently depending on the development approach.

| платформа | Triggers and bindings are configured by... |
|-------------|--------------------------------------------|
| Библиотека классов C# | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;decorating methods and parameters with C# attributes |
| All others (including Azure portal) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;updating [function.json](./functions-reference.md) ([schema](http://json.schemastore.org/function)) |

The portal provides a UI for this configuration, but you can edit the file directly by opening the **Advanced editor** available via the **Integrate** tab of your function.

In .NET, the parameter type defines the data type for input data. For instance, use `string` to bind to the text of a queue trigger, a byte array to read as binary and a custom type to de-serialize to an object.

В языках с динамическим типированием, таких как JavaScript, используйте свойство `dataType` в файле *function.json*. Например, задайте для `dataType` значение `binary`, чтобы прочитать содержимое HTTP-запроса в двоичном формате.

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Другие варианты для `dataType` — `stream` и `string`.

## <a name="binding-direction"></a>Направление привязки

Все триггеры и привязки имеют свойство `direction` в файле [function.json](./functions-reference.md):

- Для триггеров направление всегда входящее (`in`).
- Для входных и выходных привязок используется как входящее (`in`), так и исходящее (`out`) направление.
- Некоторые привязки поддерживают специальное направление `inout`. If you use `inout`, only the **Advanced editor** is available via the **Integrate** tab in the portal.

Если вы используете для настройки триггеров и привязок [атрибуты в библиотеке классов](functions-dotnet-class-library.md), направление задается в конструкторе атрибута или вычисляется по типу параметра.

## <a name="supported-bindings"></a>Поддерживаемые привязки

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Сведения о том, какие привязки доступны в предварительной версии или утверждены для использования в рабочей среде, см. в статье [Поддерживаемые языки в решении "Функции Azure"](supported-languages.md).

## <a name="resources"></a>Ресурсы
- [Binding expressions and patterns](./functions-bindings-expressions-patterns.md)
- [Using the Azure Function return value](./functions-bindings-return-value.md)
- [How to register a binding expression](./functions-bindings-register.md)
- Проверочный.
  - [Методика тестирования кода с помощью Функций Azure](functions-test-a-function.md)
  - [Запуск функции, не активируемой HTTP-запросом, вручную](functions-manually-run-non-http.md)
- [Handling binding errors](./functions-bindings-errors.md)

## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"]
> [Register Azure Functions binding extensions](./functions-bindings-register.md)
