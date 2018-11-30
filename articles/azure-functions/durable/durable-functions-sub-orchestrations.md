---
title: Вложенные оркестрации в устойчивых функциях — Azure
description: Узнайте, как вызывать оркестрации из оркестраций в расширении устойчивых функций для Функций Azure.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: azfuncdf
ms.openlocfilehash: 32f8872737fdf6dd766ae8df8ef3ed47692e2c9c
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52636969"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Вложенные оркестрации в устойчивых функциях (Функции Azure)

Кроме вызова функций действия, функции оркестратора могут вызывать другие функции оркестратора. Например, вы можете построить создать крупную оркестрацию из библиотеки функций оркестратора. Или можно запустить несколько экземпляров функции оркестратора одновременно.

Функция оркестратора может вызывать другую функцию оркестратора в ходе вызова метода [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) или [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_). Дополнительные сведения об автоматических повторных попытках см. в руководстве по [обработке ошибок и компенсации](durable-functions-error-handling.md#automatic-retry-on-failure).

Для вызывающего функции вложенного оркестратора и функции действия ведут себя одинаково. Они возвращают значение, выдают исключение и могут ожидаться родительской функцией оркестратора.

> [!NOTE]
> Методы `CallSubOrchestratorAsync` и `CallSubOrchestratorWithRetryAsync` еще не доступны в JavaScript.

## <a name="example"></a>Пример

В следующем примере показан сценарий использования Интернета вещей с несколькими устройствами, которые нужно подготовить. Для каждого из этих устройств требуется определенная оркестрация. Это может выглядеть так:

#### <a name="c"></a>C#

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    string deviceId = ctx.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await ctx.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await ctx.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await ctx.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (только для решения "Функции" версии 2)

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

Эту функцию оркестратора можно использовать как есть — для однократной подготовки устройства — или же как часть более крупной оркестрации. В последнем случае родительская функция оркестратора может планировать экземпляры `DeviceProvisioningOrchestration` с помощью API `CallSubOrchestratorAsync` (C#) или `callSubOrchestrator` (JS).

В примере ниже показано, как реализовать параллельное выполнение нескольких функций оркестратора.

#### <a name="c"></a>C#

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    string[] deviceIds = await ctx.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = ctx.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (только для решения "Функции" версии 2)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    for (const deviceId of deviceIds) {
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.push(provisionTask);
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Узнайте, что такое концентраторы задач и как их настраивать](durable-functions-task-hubs.md)
