---
title: Каналы данных телеметрии в Azure Application Insights | Документация Майкрософт
description: Как настроить каналы данных телеметрии в пакеты SDK Azure Application Insights для.NET/.NET Core.
services: application-insights
documentationcenter: .net
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: fa99e29e9ec6c2bef7296a50dd381ee5fc60a1cb
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255803"
---
# <a name="telemetrychannel-in-application-insights"></a>TelemetryChannel в Application Insights

TelemetryChannel является неотъемлемой частью [пакеты SDK Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Он управляет буферизацией и передачей данных телеметрии в службу Application Insights. .NET и .NET Core версии пакетов SDK имеют два встроенных TelemetryChannels - `InMemoryChannel` и `ServerTelemetryChannel`. В этой статье описывается каждый канал, в том числе как пользователи могут настраивать поведение канала.

## <a name="what-is-a-telemetrychannel"></a>Что такое TelemetryChannel?

`TelemetryChannel` отвечает за буферизацию и отправки элементов телеметрии Application Insights службу, где оно хранится для создания запросов и анализа. Это любой класс, реализующий интерфейс [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet)

`Send(ITelemetry item)` В конце концов вызывается метод TelemetryChannel `TelemetryInitializer`s и `TelemetryProcessor`называются s. Это означает, что все элементы, отброшенные `TelemetryProcessor` не будет достигнут канала. `Send()` не отправляет обычно элементы мгновенно к серверной части. Они обычно буферизации в памяти и отправляются в виде пакетов, для эффективной передачи данных.

[LiveMetrics](live-stream.md) также имеет пользовательского канала, который обеспечивает потоковую передачу данных телеметрии. Этот канал не зависит от канал регулярных телеметрии, и в этом документе не применяется к канала, используемого `LiveMetrics`.

## <a name="built-in-telemetrychannels"></a>Встроенные TelemetryChannels

Пакет SDK для Core Application Insights.NET/.NET поставляется с двумя встроенных каналов:

* **InMemoryChannel** 
 `InMemoryChannel` недоступно: канал, который помещает элементы в памяти, до ее отправки. Элементы помещаются в буфер в памяти и записаны на диск каждые 30 секунд или каждый раз, когда буферизировать 500 элементов. Этот канал предлагает гарантии минимальной надежности, как его повторно не отправлять данные телеметрии в случае сбоя. Этот канал не оставлять элементы на диске, поэтому любые неотправленные теряются без возможности восстановления после завершения работы приложения (правильно или нет). Существует `Flush()` метод, реализованный этот канал, который можно использовать для принудительного удаления все элементы телеметрии в памяти синхронно. Это хорошо подходит для краткосрочных приложений, где идеально синхронной Сброс.

    Этот канал поставляется как часть `Microsoft.ApplicationInsights` nuget сам пакет, а — канал по умолчанию, в пакете SDK используется при настройке ничего.

* **ServerTelemetryChannel** 
 `ServerTelemetryChannel` более сложных канал, которая содержит политики повторных попыток и можно было хранить данные на локальном диске. Этот канал повторяет отправку данных телеметрии при возникновении временных ошибок. Этот канал также использует локальное дисковое хранилище для хранения элементов на диске во время отключения сети или объемом данных телеметрии высокого уровня. Из-за эти механизмы повторных попыток и локальное дисковое хранилище этот канал считается более надежным и рекомендуется для всех рабочих сценариев. Этот канал используется по умолчанию для [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) и [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) приложений, которые настроены в соответствии с официальной документации по связанной. Этот канал оптимизирована для сценариев сервера долго выполняющихся процессов. [ `Flush()` ](#which-channel-should-i-use) Метод, реализованный этот канал не является синхронным.

    Этот канал поставляется как пакет NuGet `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`и будет подключен к автоматически при использовании любого из пакетов NuGet `Microsoft.ApplicationInsights.Web` или `Microsoft.ApplicationInsights.AspNetCore`.

## <a name="configuring-telemetrychannel"></a>Настройка TelemetryChannel

Канал телеметрии можно настроить, задав нужный `TelemetryChannel` на активной `TelemetryConfiguration`. Для приложений Asp.Net, конфигурация включает в себя параметр `TelemetryChannel` на `TelemetryConfiguration.Active`, или изменение `ApplicationInsights.config`. Для приложений ASP.NET Core конфигурация включает в себя добавление нужный канал контейнера внедрения зависимостей.

Ниже приведен пример, где пользователь настраивает `StorageFolder` для канала. `StorageFolder` является только одним из настраиваемых параметров. Полный список параметров конфигурации описан [в разделе "Параметры"](telemetry-channels.md#configurable-settings-in-channel).

## <a name="configuration-using-applicationinsightsconfig-for-aspnet-applications"></a>Настройка с помощью файла ApplicationInsights.Config для приложений ASP.NET

В следующем разделе из [ApplicationInsights.config](configuration-with-applicationinsights-config.md) показывает ServerTelemetryChannel настроены `StorageFolder` присвоено пользовательское расположение.

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!--TelemetryProcessors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

## <a name="configuration-in-code-for-aspnet-applications"></a>Конфигурация в коде для приложений ASP.NET

Следующий код устанавливает ServerTelemetryChannel с `StorageFolder` присвоено пользовательское расположение. Этот код следует добавить в начале приложения, обычно в методе Application_Start() в `Global.aspx.cs`

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

## <a name="configuration-in-code-for-aspnet-core-applications"></a>Конфигурация в коде для приложений ASP.NET Core

Изменить `ConfigureServices` метод `Startup.cs` класса, как показано ниже.

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!NOTE]
> Важно отметить, что настройка канала с помощью `TelemetryConfiguration.Active` не рекомендуется для приложений ASP.NET Core.

## <a name="configuring-telemetrychannel-in-code-for-netnet-core-console-applications"></a>Настройка TelemetryChannel в коде для.NET/.NET Core консольных приложений

Для консольных приложений кода для .NET и .NET Core используется одинаковый и показано ниже.

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Операционные сведения о ServerTelemetryChannel

`ServerTelemetryChannel` Буферов прибывающих элементов в буфере в памяти. Он сериализуется, сжимаются и сохраняются в `Transmission` экземпляра после каждые 30 секунд или когда 500 элементов помещаются в буфер. Один `Transmission` экземпляр содержит более 500 элементов и представляет собой пакет телеметрии, отправляемых через вызов одного https в службу Application Insights. По умолчанию может быть более 10 `Transmission`s, отправляемых в параллельном режиме. Если данные телеметрии, поступающих более высоким скоростям или серверной части сети/Application Insights работает медленно, затем `Transmission`s сохраняются в памяти. Емкости этого буфера передачи в памяти по умолчанию составляет 5 МБ. После превышает емкость в памяти, `Transmission`сохраняются на локальном диске для 50 МБ. `Transmission`s сохраняются на локальном диске, когда существуют также проблемы с сетью. Только элементы, хранящиеся на локальном диске избежать простоев в случае сбоя приложения, которые отправляются при каждом повторном запуске приложения.

## <a name="configurable-settings-in-channel"></a>Настраиваемые параметры канала

Полный список настраиваемых параметров для каждого канала приведены здесь.

[InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

[ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Наиболее часто используемые настройки для `ServerTelemetryChannel` , перечислены ниже:

1. `MaxTransmissionBufferCapacity` -Максимальный объем памяти в байтах, используемый каналом для буферизации операций обмена данными в памяти. По достижении этой емкости новые элементы будут храниться непосредственно на локальный диск. Значение по умолчанию составляет 5 МБ. Задание потенциальных клиентов более высокое значение меньше на диске, но он является важно отметить, что элементы в памяти будут потеряны, если приложение аварийно завершает работу.

2. `MaxTransmissionSenderCapacity` -Максимальный объем `Transmission`s, которое будет отправляться в Application Insights, в то же время. Значение по умолчанию — 10, но его можно установить более высокое значение. Это рекомендуется, когда создается большой объем данных телеметрии, обычно при выполнении нагрузочного тестирования и/или когда выборки выключена.

3. `StorageFolder` — Папка, используемого каналом для хранения элементов на диск при необходимости. В Windows используется % LocalAppData % или % Temp %, если ничего не настроено явно. В средах Non-Windows пользователя **должен** Настройка допустимое расположение, без которых данные телеметрии не будут храниться на локальном диске.

## <a name="which-channel-should-i-use"></a>Какой канал следует использовать?

`ServerTelemetryChannel` рекомендуется для большинства рабочих сценариев долго выполняющихся приложений. `Flush()` Реализацию метода `ServerTelemetryChannel` не является синхронным, и `Flush()` не гарантирует отправку всех ожидающие элементы из памяти или диска. Если этот канал используется в сценариях, где приложение собирается завершить работу, а затем рекомендуется сделать после вызова метода некоторая задержка `Flush()` по этому каналу. Точный объем задержка не является прогнозируемым, так как он зависит от факторов, таких как количество элементов или `Transmissions` находятся в памяти, сколько имеется на диске, сколько уже получен для копирования, и если канал находится в середине сценарии экспоненциальной отсрочки. Если нужно выполнять синхронные Сброс, затем `InMemoryChannel` рекомендуется.

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="does-applicationinsights-channel-offer-guaranteed-telemetry-delivery-or-what-are-the-scenarios-where-telemetry-can-be-lost"></a>*Предлагает гарантированную телеметрии доставки или сценарии, где возможна потеря данных телеметрии ApplicationInsights канал?*

* Короткий ответ — ни один из встроенных каналов обеспечивают транзакции типа гарантирован доставки данных телеметрии в серверную часть. Хотя `ServerTelemetryChannel` является более сложные по сравнению с `InMemoryChannel` предоставление надежные данные телеметрии, таким образом, наиболее безопасным местом для отправки данных телеметрии и телеметрии по-прежнему может быть потеряна в ряде сценариев. Ниже перечислены некоторые из распространенных сценариев, где данные телеметрии теряется.

1. Элементы в памяти теряются при сбое приложения.
1. Данные телеметрии хранится на локальном диске во время отключения сети или проблемы с серверной частью Application Insights. Тем не менее элементы, созданные более 24 часов, удаляются. Поэтому телеметрии теряется во время длительного периода проблемы с сетью.
1. Расположения диска по умолчанию для хранения данных телеметрии в Windows: % LocalAppData % или % Temp %. Эти места находятся на обычно локального компьютера. Если приложение выполняет миграцию физически из одного расположения в другое, любые данные телеметрии, хранятся в этом расположении будет потеряно.
1. Веб-приложений Azure (Windows) расположение диска по умолчанию — «D:\local\LocalAppData». Это расположение не сохраняются и удаление приведет к перезагрузке приложения, масштаб схемы и т. д., что приводит к потере данных телеметрии, хранящиеся в этих расположениях. Пользователи могут переопределить хранилища материализованных расположении, например «D:\home», но эти сохраненные места под обслуживаются удаленного хранилища и может выполняться медленно.

### <a name="does-servertelemetrychannel-work-in-non-windows-systems"></a>*ServerTelemetryChannel работает в системах, отличных от Windows?*

* Несмотря на название пакета/пространства имен, WindowsServer этот канал поддерживается в системах без Windows со следующим исключением. В не Windows канал не создает папку локального хранилища по умолчанию. Пользователям необходимо создать папку локального хранилища и настроить канал для его использования. После настройки локального хранилища канал работает так же в Windows и систем, отличных от Windows.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>*Пакет SDK приводит к созданию временное локальное хранилище? Данные шифруются в хранилище?*

* Пакет SDK хранит элементы телеметрии в локальном хранилище, во время проблемы с сетью или во время регулирования. Эти данные не шифруется локально.
Для систем Windows пакет SDK автоматически создает временный локальную папку в каталоге TEMP или APPDATA и ограничивает доступ для администраторов и только для текущего пользователя.
Для Non-Windows не локальное хранилище создается автоматически с помощью пакета SDK, и поэтому данные не хранятся по умолчанию. Пользователей можно самостоятельно создать каталог хранилища и настроить канал для его использования. В этом случае пользователь несет ответственность за соблюдение защиту этого каталога.
Дополнительные сведения о [защиты данных и конфиденциальности](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>Пакет SDK с открытым исходным кодом
Как и каждый пакеты SDK Application Insights каналы также являются открытым исходным кодом. Чтение и вносить дополнения в код или сообщить о проблемах в [официальный репозиторий GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Следующие шаги

* [Выборка](../../azure-monitor/app/sampling.md)
* [Устранение неполадок пакета SDK](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
