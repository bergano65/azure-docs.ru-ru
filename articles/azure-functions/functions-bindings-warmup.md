---
title: Azure Functions warmup trigger
description: Understand how to use the warmup trigger in Azure Functions.
documentationcenter: na
author: alexkarcher-msft
manager: gwallace
keywords: azure functions, functions, event processing, warmup, cold start, premium, dynamic compute, serverless architecture
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2019
ms.author: alkarche
ms.openlocfilehash: 6884c8f1bf7a462b5d93f5c9ea23a2f64021fd9e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328499"
---
# <a name="azure-functions-warm-up-trigger"></a>Azure Functions warm-up trigger

This article explains how to work with the warmup trigger in Azure Functions. The warmup trigger is supported only for function apps running in a [Premium plan](functions-premium-plan.md). A warmup trigger  is invoked when an instance is added to scale a running function app. You can use a warmup trigger to pre-load custom dependencies during the [pre-warming process](./functions-premium-plan.md#pre-warmed-instances) so that your functions are ready to start processing requests immediately. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Пакеты — Функции 2.x

The [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet package, version **3.0.5 or higher** is required. Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/). 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Триггер

The warmup trigger lets you define a function that will be run on an instance when it is added to your running app. You can use a warmup function to open connections, load dependencies, or run any other custom logic before your app will begin receiving traffic. 

The warmup trigger is intended to create shared dependencies that will be used by the other functions in your app. [See examples of shared dependencies here](./manage-connections.md#client-code-examples).

Note that the warmup trigger is only called during scale-up operations, not during restarts or other non-scale startups. You must ensure your logic can load all necessary dependencies without using the warmup trigger. Lazy loading is a good pattern to achieve this.

## <a name="trigger---example"></a>Пример триггера

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

The following example shows a [C# function](functions-dotnet-class-library.md) that will run on each new instance when it is added to your app. A return value attribute isn't required.


* Your function must be named ```warmup``` (case-insensitive) and there may only be one warmup function per app.
* To use warmup as a .NET class library function, please make sure you have a package reference to **Microsoft.Azure.WebJobs.Extensions >= 3.0.5**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


Placeholder comments show where in the application to declare and initialize shared dependencies. 
[Learn more about shared dependencies here](./manage-connections.md#client-code-examples).

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
 
namespace WarmupSample
{

    //Declare shared dependencies here

    public static class Warmup
    {
        [FunctionName("Warmup")]
        public static void Run([WarmupTrigger()] WarmupContext context,
            ILogger log)
        {
            //Initialize shared dependencies here
            
            log.LogInformation("Function App instance is warm 🌞🌞🌞");
        }
    }
}
```
# <a name="c-scripttabcsharp-script"></a>[C# Script](#tab/csharp-script)


The following example shows a warmup trigger in a *function.json* file and a [C# script function](functions-reference-csharp.md) that will run on each new instance when it is added to your app.

Your function must be named ```warmup``` (case-insensitive), and there may only be one warmup function per app.

Ниже показан файл *function.json*.

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

В разделе [Конфигурация](#trigger---configuration) описываются эти свойства.

Ниже приведен код сценария C#, который выполняет привязку к `HttpRequest`:

```cs
public static void Run(ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

The following example shows a warmup trigger in a *function.json* file and a [JavaScript function](functions-reference-node.md)  that will run on each new instance when it is added to your app.

Your function must be named ```warmup``` (case-insensitive) and there may only be one warmup function per app.

Ниже показан файл *function.json*.

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

В разделе [Конфигурация](#trigger---configuration) описываются эти свойства.

Ниже показан код JavaScript.

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

The following example shows a warmup trigger in a *function.json* file and a [Python function](functions-reference-python.md) that will run on each new instance when it is added to your app.

Your function must be named ```warmup``` (case-insensitive) and there may only be one warmup function per app.

Ниже показан файл *function.json*.

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

В разделе [Конфигурация](#trigger---configuration) описываются эти свойства.

Ниже приведен код Python.

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="javatabjava"></a>[Java](#tab/java)

The following example shows a warmup trigger in a *function.json* file and a [Java functions](functions-reference-java.md)  that will run on each new instance when it is added to your app.

Your function must be named ```warmup``` (case-insensitive) and there may only be one warmup function per app.

Ниже показан файл *function.json*.

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

Ниже приведен код Java.

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>Атрибуты триггера

In [C# class libraries](functions-dotnet-class-library.md), the `WarmupTrigger` attribute is available to configure the function.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

This example demonstrates how to use the [warmup](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs) attribute.

Note that your function must be called ```Warmup``` and there can only be one warmup function per app.

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

For a complete example, see the [trigger example](#trigger---example).

# <a name="c-scripttabcsharp-script"></a>[C# Script](#tab/csharp-script)

Attributes are not supported by C# Script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Attributes are not supported by JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Attributes are not supported by Python.

# <a name="javatabjava"></a>[Java](#tab/java)

The warmup trigger is not supported in Java as an attribute.

---

## <a name="trigger---configuration"></a>Конфигурация триггера

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `WarmupTrigger`.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
| **type** | Н/Д| Обязательное. Необходимо задать значение `warmupTrigger`. |
| **direction** | Н/Д| Обязательное. Необходимо задать значение `in`. |
| **name** | Н/Д| Required - the variable name used in function code.|

## <a name="trigger---usage"></a>Использование триггера

No additional information is provided to a warmup triggered function when it is invoked.

## <a name="trigger---limits"></a>Триггер — ограничения

* The warmup trigger is only available to apps running on the [Premium plan](./functions-premium-plan.md).
* The warmup trigger is only called during scale up operations, not during restarts or other non-scale startups. You must ensure your logic can load all necessary dependencies without using the warmup trigger. Lazy loading is a good pattern to achieve this.
* The warmup trigger cannot be invoked once an instance is already running.
* There can only be one warmup trigger function per function app.

## <a name="next-steps"></a>Дальнейшие действия

[Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)
