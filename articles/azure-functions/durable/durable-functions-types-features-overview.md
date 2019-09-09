---
title: Типы функций и функции в Устойчивые функции расширении функций Azure
description: Сведения о типах функций и ролей, которые поддерживают обмен данными между функциями в Устойчивые функции оркестрации в функциях Azure.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 0d3087c768a02bb5c647fc0d10db3aa4274804f4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097742"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Типы и функции Устойчивые функции (функции Azure)

Устойчивые функции является расширением [функций Azure](../functions-overview.md). Устойчивые функции можно использовать для согласования состояния выполнения функций. Устойчивая функция — это решение, которое состоит из различных функций Azure. Функции могут играть различные роли в устойчивой функции оркестрации. 

В этой статье приводятся общие сведения о типах функций, которые можно использовать в Устойчивые функции оркестрации. Статья содержит некоторые общие шаблоны, которые можно использовать для подключения функций. Узнайте, как Устойчивые функции может помочь в решении задач разработки приложений.

![Изображение, показывающее типы устойчивых функций][1]  

## <a name="types-of-durable-functions"></a>Типы устойчивых функций

В функциях Azure можно использовать четыре типа устойчивых функций: Activity, Orchestrator, Entity и Client.

### <a name="activity-functions"></a>Функции действий

Функции действий — это базовая единица работы в устойчивой функции оркестрации. Функции действий — это функции и задачи, управляемые в процессе. Например, вы можете создать устойчивую функцию для обработки заказа. Задачи подразумевают проверку запасов, оплату клиента и создание отгрузки. Каждая задача является функцией действия. 

Функции действий не ограничиваются в типе работы, которую можно выполнять в них. Функцию действия можно написать на любом [языке, который устойчивые функции поддерживать](durable-functions-overview.md#language-support). Платформа устойчивых задач гарантирует, что каждая вызываемая функция действия будет выполнена во время оркестрации по меньшей мере один раз.

Используйте [триггер действия](durable-functions-bindings.md#activity-triggers) для активации функции действия. Функции .NET получают [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) в качестве параметра. Триггер можно также привязать к любому другому объекту для передачи входных данных в функцию. В JavaScript можно получить доступ к входным данным через `<activity trigger binding name>` свойство [ `context.bindings` объекта](../functions-reference-node.md#bindings).

Функция действия может также возвращать значения в Orchestrator. При отправке или возврате большого количества значений из функции действия можно использовать [кортежи или массивы](durable-functions-bindings.md#passing-multiple-parameters). Функцию действия можно активировать только из экземпляра оркестрации. Несмотря на то, что функция действия и другая функция (например, функция, активируемая HTTP) могут совместно использовать некоторый код, каждая функция может иметь только один триггер.

Дополнительные сведения и примеры см. в разделе [функции действий](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Функции оркестратора

Функции Orchestrator описывают выполнение действий и порядок выполнения действий. Функции Orchestrator описывают оркестрации в коде (C# или JavaScript), как показано в [устойчивые функции шаблонах и технических понятиях](durable-functions-concepts.md). Оркестрации может иметь множество различных типов действий, включая [функции действий](#activity-functions), [подсогласования](#sub-orchestrations), [ожидание внешних событий](#external-events)и [таймеров](#durable-timers). Функции Orchestrator также могут взаимодействовать с [функциями сущностей](#entity-functions).

Функция оркестратора должена активироваться [триггером оркестрации](durable-functions-bindings.md#orchestration-triggers).

Orchestrator запускается [клиентом Orchestrator](#client-functions). Вы можете активировать Orchestrator из любого источника (HTTP, очередь, поток событий). Каждый экземпляр оркестрации имеет идентификатор экземпляра. Идентификатор экземпляра может быть автоматически сформирован (рекомендуется) или создан пользователем. Идентификатор экземпляра можно использовать для [управления экземплярами](durable-functions-instance-management.md) оркестрации.

Дополнительные сведения и примеры см. в разделе [триггеры оркестрации](durable-functions-bindings.md#orchestration-triggers).

###  <a name="entity-functions"></a>Функции сущности (Предварительная версия)

Функции сущностей определяют операции чтения и обновления мелких частей состояния, известных как *устойчивые сущности*. Как и функции Orchestrator, функции сущностей — это функции с особым типом триггера, *триггером сущности*. В отличие от функций Orchestrator, функции сущностей не имеют конкретных ограничений кода. Функции сущностей также управляют состоянием явным образом, а не неявно представляют состояние с помощью потока управления.

> [!NOTE]
> Функции сущностей и связанные функции доступны только в Устойчивые функции 2,0 и более поздних версиях.

Дополнительные сведения о функциях сущностей см. в документации по функциям в предварительной версии [функций сущностей](durable-functions-preview.md#entity-functions) .

### <a name="client-functions"></a>Клиентские функции

Клиентские функции — это активируемые функции, которые создают экземпляры оркестрации и сущности и управляют ими. Они фактически являются точкой входа для взаимодействия с Устойчивые функции. Клиентскую функцию можно активировать из любого источника (HTTP, очередь, поток событий и т. д.). Клиентская функция использует [привязку клиента оркестрации](durable-functions-bindings.md#orchestration-client) для создания устойчивых оркестрации и сущностей и управления ими.

Наиболее простой пример клиентской функции — это функция, активируемая по протоколу HTTP, которая запускает функцию Orchestrator, а затем возвращает ответ о состоянии проверки. Пример см. в разделе [обнаружение URL-адреса API HTTP](durable-functions-http-api.md#http-api-url-discovery).

Дополнительные сведения и примеры см. в разделе [клиент оркестрации](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Возможности и шаблоны

В следующих разделах описываются функции и шаблоны типов Устойчивые функции.

### <a name="sub-orchestrations"></a>Вложенные оркестрации

Функции Orchestrator могут вызывать функции действий, но они также могут вызывать другие функции Orchestrator. Например, вы можете построить создать крупную оркестрацию из библиотеки функций оркестратора. Кроме того, можно параллельно запускать несколько экземпляров функции Orchestrator.

Дополнительные сведения и примеры см. в разделе [взаимосогласования](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Устойчивые таймеры

[Устойчивые функции](durable-functions-overview.md) предоставляет *устойчивые таймеры* , которые можно использовать в функциях Orchestrator для реализации задержек или настройки времени ожидания асинхронных действий. Используйте устойчивые таймеры в функциях Orchestrator `Thread.Sleep` вместо `Task.Delay` иC#() `setTimeout()` или `setInterval()` и (JavaScript).

Дополнительные сведения и примеры см. в разделе [устойчивые таймеры](durable-functions-timers.md).

### <a name="external-events"></a>Внешние события

Функции оркестратора могут ожидать передачи внешних событий для обновления экземпляра оркестрации. Эта функция Устойчивые функции часто полезна для обработки взаимодействия человека или других внешних обратных вызовов.

Дополнительные сведения и примеры см. в разделе [внешние события](durable-functions-external-events.md).

### <a name="error-handling"></a>Обработка ошибок

Используйте код для реализации оркестрации Устойчивые функции. Можно использовать функции обработки ошибок языка программирования. Такие шаблоны `try` , как / `catch` работа в оркестрации. 

Устойчивые функции также поставляются со встроенными политиками повтора. Действие может задержать и повторять действия автоматически при возникновении исключения. Вы можете использовать повторные попытки для управления временными исключениями без прерывания оркестрации.

Дополнительные сведения и примеры см. в разделе [Обработка ошибок](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Взаимодействие между приложениями-функциями

Хотя устойчивое согласование выполняется в контексте одного приложения-функции, можно использовать закономерности для координации оркестрации во многих приложениях-функциях. Обмен данными между приложениями может происходить по протоколу HTTP, но использование устойчивой платформы для каждого действия означает, что вы все равно можете обслуживать устойчивый процесс в двух приложениях.

В следующих примерах демонстрируется координация приложения между функциями C# в и JavaScript. В каждом примере одно действие запускает внешнее согласование. Другое действие извлекает и возвращает состояние. Orchestrator ждет состояния `Complete` , прежде чем продолжить.

Ниже приведены некоторые примеры оркестрации в приложении с кросс-функциями.

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

## <a name="next-steps"></a>Следующие шаги

Чтобы приступить к работе, создайте свою первую устойчивую функцию в [C#](durable-functions-create-first-csharp.md) или [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Подробнее о Устойчивые функции](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
