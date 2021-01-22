---
title: Включение Snapshot Debugger для приложений .NET и .NET Core в функциях Azure | Документация Майкрософт
description: Включение Snapshot Debugger для приложений .NET и .NET Core в функциях Azure
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 12/18/2020
ms.openlocfilehash: d86455eae0834f29099c7d5c96f8326408daf519
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98675535"
---
# <a name="enable-snapshot-debugger-for-net-and-net-core-apps-in-azure-functions"></a>Включение Snapshot Debugger для приложений .NET и .NET Core в функциях Azure

Snapshot Debugger в настоящее время работает для приложений ASP.NET и ASP.NET Core, которые работают на основе функций Azure в планах служб Windows.

При использовании Snapshot Debugger рекомендуется запускать приложение на уровне службы "базовый" или выше.

Для большинства приложений уровни обслуживания "бесплатный" и "общий" не содержат достаточно памяти или места на диске для сохранения моментальных снимков.

## <a name="prerequisites"></a>Предварительные условия

* [Включение мониторинга Application Insights в приложение-функция](../../azure-functions/configure-monitoring.md#add-to-an-existing-function-app)

## <a name="enable-snapshot-debugger"></a>Включить Snapshot Debugger

Если вы используете другой тип службы Azure, ниже приведены инструкции по включению Snapshot Debugger на других поддерживаемых платформах:
* [служба приложений Azure](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json);
* [Oблачныe службы Azure2](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Службы Service Fabric Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Профилирование веб-приложений, работающих на виртуальной машине Azure или в масштабируемом наборе виртуальных машин, с помощью Application Insights Profiler](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Локальные виртуальные или физические компьютеры](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Чтобы включить Snapshot Debugger в приложении функции, необходимо обновить `host.json` файл, добавив свойство, `snapshotConfiguration` как указано ниже, и повторно развернуть функцию.

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

Snapshot Debugger предварительно устанавливается в составе среды выполнения функций Azure, которая по умолчанию отключена.

Поскольку Snapshot Debugger он входит в среду выполнения функций Azure, не требуется добавлять дополнительные пакеты NuGet и параметры приложения.

Как и ссылка, для простого приложения-функции (.NET Core) ниже показано, как это будет выглядеть `.csproj` , `{Your}Function.cs` и `host.json` после включения snapshot Debugger.

CSPROJ проекта

```xml
<Project Sdk="Microsoft.NET.Sdk">
<PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
    <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.31" />
</ItemGroup>
<ItemGroup>
    <None Update="host.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
    <None Update="local.settings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
    </None>
</ItemGroup>
</Project>
```

Класс Function

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace SnapshotCollectorAzureFunction
{
    public static class ExceptionFunction
    {
        [FunctionName("ExceptionFunction")]
        public static Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            throw new NotImplementedException("Dummy");
        }
    }
}
```

Файл узла

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

## <a name="disable-snapshot-debugger"></a>Отключить Snapshot Debugger

Чтобы отключить Snapshot Debugger в приложении функции, необходимо просто обновить `host.json` файл, задав для `false` свойства значение `snapshotConfiguration.isEnabled` .

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": false
      }
    }
  }
}
```

Рекомендуется включить Snapshot Debugger для всех приложений, чтобы упростить диагностику исключений приложений.

## <a name="next-steps"></a>Следующие шаги

- Создание трафика для приложения, которое может вызвать исключение. Затем подождите 10 – 15 минут, чтобы моментальные снимки отправлялись на экземпляр Application Insights.
- [Просмотр моментальных снимков](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) в портал Azure.
- Настройте конфигурацию Snapshot Debugger на основе вашего варианта использования в приложении-функции. Дополнительные сведения см. [в разделе Настройка моментальных снимков в host.js](../../azure-functions/functions-host-json.md#applicationinsightssnapshotconfiguration).
- Справку по устранению неполадок Snapshot Debugger см. в разделе [snapshot Debugger устранение неполадок](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).