---
title: Вложенные оркестрации в устойчивых функциях — Azure
description: Узнайте, как вызывать оркестрации из оркестраций в расширении устойчивых функций для Функций Azure.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/07/2019
ms.author: azfuncdf
ms.openlocfilehash: 7b5e811daecbb7687abe7a37b75e2730d7830c2c
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983618"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Вложенные оркестрации в устойчивых функциях (Функции Azure)

Кроме вызова функций действия, функции оркестратора могут вызывать другие функции оркестратора. Например, вы можете построить создать крупную оркестрацию из библиотеки функций оркестратора. Или можно запустить несколько экземпляров функции оркестратора одновременно.

Функция оркестратора может вызывать другую функцию оркестратора в ходе вызова методов [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) или [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) (.NET) и `callSubOrchestrator` или `callSubOrchestratorWithRetry` (JavaScript). Дополнительные сведения об автоматических повторных попытках см. в руководстве по [обработке ошибок и компенсации](durable-functions-error-handling.md#automatic-retry-on-failure).

Для вызывающего функции вложенного оркестратора и функции действия ведут себя одинаково. Они возвращают значение, выдают исключение и могут ожидаться родительской функцией оркестратора. 

> [!NOTE]
> В настоящее время необходимо предоставить `instanceId` значение аргумента API подоркестрацииа в JavaScript.

## <a name="example"></a>Пример

В следующем примере показан сценарий использования Интернета вещей с несколькими устройствами, которые нужно подготовить. Для каждого из этих устройств требуется определенная оркестрация. Это может выглядеть так:

### <a name="c"></a>C#

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string deviceId = context.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await context.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await context.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await context.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceId = context.df.getInput();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    const sasUrl = yield context.df.callActivity("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    yield context.df.callActivity("SendPackageUrlToDevice", { id: deviceId, url: sasUrl });

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.df.waitForExternalEvent("DownloadCompletedAck");

    // Step 4: ...
});
```

Эту функцию оркестратора можно использовать как есть — для однократной подготовки устройства — или же как часть более крупной оркестрации. В последнем случае родительская функция оркестратора может планировать экземпляры `DeviceProvisioningOrchestration` с помощью API `CallSubOrchestratorAsync` (C#) или `callSubOrchestrator` (JavaScript).

В примере ниже показано, как реализовать параллельное выполнение нескольких функций оркестратора.

### <a name="c"></a>C#

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string[] deviceIds = await context.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = context.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    var id = 0;
    for (const deviceId of deviceIds) {
        const child_id = context.df.instanceId+`:${id}`;
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId, child_id);
        provisioningTasks.push(provisionTask);
        id++;
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

> [!NOTE]
> Подсогласования должны быть определены в том же приложении-функции, что и родительское согласование. Если необходимо вызвать и подождать согласования в другом приложении-функции, рассмотрите возможность использования встроенной поддержки API HTTP и шаблона потребителя опроса HTTP 202. Дополнительные сведения см. в разделе [функции HTTP](durable-functions-http-features.md) .

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Узнайте, как задать пользовательское состояние оркестрации](durable-functions-custom-orchestration-status.md)