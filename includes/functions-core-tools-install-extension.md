---
title: включение файла
description: включение файла
services: functions
author: craigshoemaker
ms.service: functions
ms.topic: include
ms.date: 09/25/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: fc5b43dcdee394fea023124171fb42c1a18224dc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131461"
---
Пакеты расширения сделать все привязки, опубликованных группой функций Azure, доступные посредством параметра в *host.json* файла. Для локальной разработки гарантировать использование последней версии [основных инструментов функций Azure](../articles/azure-functions/functions-run-local.md#install-the-azure-functions-core-tools).

Чтобы использовать пакеты расширения, обновите *host.json* файл, чтобы включить следующую запись для `extensionBundle`:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

- `id` Ссылается на пространство имен для пакетов расширения функций Microsoft Azure.
- `version` Ссылается на версию пакета.

Увеличение версии пакета как пакеты в пакет изменений. Основной номер версии изменения вступают в силу только в том случае, когда пакеты в пакете перемещает основной номер версии. `version` Используется в свойстве [записью интервал для указания диапазонов версий](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards). Среда выполнения функций всегда выбирает максимальную допустимую версию определяется диапазон версий или интервал.

После расширения пакеты ссылаетесь в своем проекте, все привязки по умолчанию становятся доступными для ваших функций. Привязки, доступные в [пакета расширения](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) являются:

|Пакет  |Version  |
|---------|---------|
|Microsoft.Azure.WebJobs.Extensions.CosmosDB|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.DurableTask|1.8.0|
|Microsoft.Azure.WebJobs.Extensions.EventGrid|2.0.0|
|Microsoft.Azure.WebJobs.Extensions.EventHubs|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SendGrid|3.0.0|
|Microsoft.Azure.WebJobs.Extensions.ServiceBus|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SignalRService|1.0.0|
|Microsoft.Azure.WebJobs.Extensions.Storage|3.0.4|
|Microsoft.Azure.WebJobs.Extensions.Twilio|3.0.0|