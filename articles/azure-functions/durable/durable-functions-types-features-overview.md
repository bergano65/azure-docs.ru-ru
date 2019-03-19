---
title: Типы функций и функций в расширении устойчивых функций, функций Azure
description: Дополнительные сведения о типах функций и ролей, которые поддерживают функции к функции взаимодействия в оркестрации устойчивых функций в функциях Azure.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 76b6f013333113d5a24b744bc962d36b1c0e21b3
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455740"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Устойчивые функции типы и функции (функции Azure)

Устойчивые функции — это расширение ["функции Azure"](../functions-overview.md). Устойчивые функции можно использовать для оркестрации с отслеживанием состояния выполнения функции. Устойчивые функции — это решение, состоит из различных функций Azure. Функции может играть разные роли в оркестрации устойчивых функций. 

Этой статье приводится обзор типов функций, которые можно использовать в оркестрации устойчивых функций. В этой статье содержатся некоторые распространенные шаблоны, которые можно использовать для подключения функций. Узнайте, как устойчивых функций может помочь решить проблемы разработки приложений.

![Образ, который отображает типы устойчивых функций][1]  

## <a name="types-of-durable-functions"></a>Типы устойчивых функций

Можно использовать три типа устойчивые функции в функциях Azure: действие, orchestrator и клиента.

### <a name="activity-functions"></a>Функции действий

Функции действий являются базовой единицей работы в оркестрации устойчивых функций. Функции действий являются функции и задачи, организованные в процессе. Например можно создать устойчивую функцию для обработки заказа. Задачи включают в себя проверки запасов, начисляется плата за клиента и создания отгрузки. Каждая задача бы функция действия. 

Функции действий не ограничено только в тип работы, которые можно выполнить в них. Функция действия можно написать на любом [язык, устойчивые функции поддерживают](durable-functions-overview.md#language-support). Платформа устойчивых задач гарантирует, что каждая вызываемая функция действия будет выполнена во время оркестрации по меньшей мере один раз.

Используйте [триггер действия](durable-functions-bindings.md#activity-triggers) для активации функции действия. Получение функций .NET [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) как параметр. Триггер можно также привязать к любому другому объекту для передачи входных данных в функцию. На JavaScript, вы можете воспользоваться входных данных с помощью `<activity trigger binding name>` свойство [ `context.bindings` объект](../functions-reference-node.md#bindings).

Действие функции также могут возвращать значения для оркестратора. Если отправка и возвращать большое количество значений из функции действия, можно использовать [кортежей или массивы](durable-functions-bindings.md#passing-multiple-parameters). Вы можете активировать функцию действия только от экземпляра оркестрации. Несмотря на то, что функция действия и другой функции (например, функцию, активируемую HTTP) могут совместно использовать часть кода, каждая функция может иметь только один триггер.

Дополнительные сведения и примеры см. в разделе [функции действий](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Функции оркестратора

Функции оркестратора описания выполнения действия и порядок, в котором выполняются действия. Функции оркестратора описывают оркестрации в коде (C# или JavaScript) как показано в [устойчивых функций шаблонов и технические концепции](durable-functions-concepts.md). Управляющий элемент может иметь множество различных типов действий, включая [функции действий](#activity-functions), [суборкестрации](#sub-orchestrations), [ожидание внешних событий](#external-events)и [таймеры](#durable-timers). 

Функция оркестратора должена активироваться [триггером оркестрации](durable-functions-bindings.md#orchestration-triggers).

Оркестратор запущена с [orchestrator клиента](#client-functions). Вы можете активировать orchestrator из любого источника (HTTP, очередь, поток событий). Каждый экземпляр оркестрации имеет идентификатор экземпляра. Идентификатор экземпляра может быть автоматически (рекомендуется) или созданным пользователем. Можно использовать идентификатор экземпляра для [управления экземплярами](durable-functions-instance-management.md) оркестрации.

Дополнительные сведения и примеры см. в разделе [триггеры Оркестрации](durable-functions-bindings.md#orchestration-triggers).

### <a name="client-functions"></a>Клиентские функции

Функции клиента являются активированные функции, создающие новые экземпляры оркестрации. Функции клиента — точка входа для создания экземпляра оркестрации устойчивых функций. Вы можете активировать на клиентскую функцию из любого источника (HTTP, очередь, поток событий). На клиентскую функцию можно написать на любом языке, который поддерживает приложение. 

Функции клиента также имеют [клиента оркестрации](durable-functions-bindings.md#orchestration-client) привязки. На клиентскую функцию можно использовать привязку клиента оркестрации для создания и управления ими устойчивой оркестрации. 

Самый простой пример функции, клиент — это функция, активируемых по HTTP, которая запускает функции оркестратора, а затем возвращает ответ состояния проверки. Например, см. в разделе [URL-адрес HTTP API обнаружения](durable-functions-http-api.md#http-api-url-discovery).

Дополнительные сведения и примеры см. в разделе [клиента Оркестрации](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Возможности и шаблоны

В следующих разделах описываются функции и шаблоны типов устойчивых функций.

### <a name="sub-orchestrations"></a>Вложенные оркестрации

Функции оркестратора могут вызывать функции действий, но они также могут вызывать другие функции оркестратора. Например, вы можете построить создать крупную оркестрацию из библиотеки функций оркестратора. Или можно запустить несколько экземпляров функции оркестратора в параллельном режиме.

Дополнительные сведения и примеры см. в разделе [суборкестрации](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Устойчивые таймеры

[Устойчивые функции](durable-functions-overview.md) предоставляет *устойчивые таймеры* , можно использовать в функциях оркестраторов для реализации задержек или настройки времени ожидания в асинхронных действиях. Использовать устойчивые таймеры в функциях оркестраторов вместо `Thread.Sleep` и `Task.Delay` (C#) или `setTimeout()` и `setInterval()` (JavaScript).

Дополнительные сведения и примеры см. в разделе [устойчивые таймеры](durable-functions-timers.md).

### <a name="external-events"></a>Внешние события

Функции оркестратора могут ожидать передачи внешних событий для обновления экземпляра оркестрации. Эта функция устойчивых функций часто полезна для обработки действий человека или другие внешние обратные вызовы.

Дополнительные сведения и примеры см. в разделе [внешних событий](durable-functions-external-events.md).

### <a name="error-handling"></a>Обработка ошибок

Используйте код, чтобы реализовать оркестрации устойчивых функций. Можно использовать функции обработки ошибок языка программирования. Шаблоны, например `try` / `catch` рабочей во взаимодействии. 

Устойчивые функции также поставляются с политиками встроенный механизм повторных попыток. Действие можно отложить и повторного исполнения действий автоматически при возникновении исключения. Повторных попыток можно использовать для обработки временных исключений без оставления оркестрации.

Дополнительные сведения и примеры см. в разделе [обработка ошибок](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Взаимодействие между приложениями-функциями

Несмотря на то, что устойчивой оркестрации выполняется в контексте приложения одной функции, можно использовать шаблоны для координации взаимодействий для многих приложений-функций. Обмен данными между приложениями могут возникнуть по протоколу HTTP, но с помощью платформы устойчивых для каждого действия означает, что можно по-прежнему поддерживать устойчивых процесс между двумя приложениями.

В следующих примерах демонстрируется приложение-функцию перекрестной оркестрации в C# и JavaScript. В каждом примере одно действие запускает внешней оркестрации. Другое действие извлекает и возвращает статус. Оркестратор ожидает состояния в `Complete` перед продолжением.

Ниже приведены некоторые примеры приложений кросс функция оркестрации.

#### <a name="c"></a>C#

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration.
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now, go do more work...
}

[FunctionName("StartRemoteOrchestration")]
public static async Task<string> StartRemoteOrchestration([ActivityTrigger] string orchestratorName)
{
    using (var response = await HttpClient.PostAsync(
        $"https://appB.azurewebsites.net/orchestrations/{orchestratorName}",
        new StringContent("")))
    {
        string statusUrl = await response.Content.ReadAsAsync<string>();
        return statusUrl;
    }
}

[FunctionName("CheckIsComplete")]
public static async Task<bool> CheckIsComplete([ActivityTrigger] string statusUrl)
{
    using (var response = await HttpClient.GetAsync(statusUrl))
    {
        // 200 = Complete, 202 = Running
        return response.StatusCode == HttpStatusCode.OK;
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    // Do some work...

    // Call a remote orchestration.
    const statusUrl = yield context.df.callActivity("StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true) {
        const isComplete = yield context.df.callActivity("CheckIsComplete", statusUrl);
        if (isComplete) {
            break;
        }

        const waitTime = moment(context.df.currentUtcDateTime).add(1, "m").toDate();
        yield context.df.createTimer(waitTime);
    }

    // B is done. Now, go do more work...
});
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, orchestratorName) {
    const options = {
        method: "POST",
        uri: `https://appB.azurewebsites.net/orchestrations/${orchestratorName}`,
        body: ""
    };

    const statusUrl = await request(options);
    return statusUrl;
};
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, statusUrl) {
    const options = {
        method: "GET",
        uri: statusUrl,
        resolveWithFullResponse: true,
    };

    const response = await request(options);
    // 200 = Complete, 202 = Running
    return response.statusCode === 200;
};
```

## <a name="next-steps"></a>Дальнейшие действия

Чтобы приступить к работе, создайте ваша устойчивая функция в [ C# ](durable-functions-create-first-csharp.md) или [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Дополнительные сведения об устойчивых функций](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
