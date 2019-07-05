---
title: Каналы данных телеметрии в Azure Application Insights | Документация Майкрософт
description: Как настроить каналы данных телеметрии в пакеты SDK Azure Application Insights для .NET и .NET Core.
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
ms.openlocfilehash: af00641123354831c7bf174a743ded2886343579
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561348"
---
# <a name="telemetry-channels-in-application-insights"></a>Каналы данных телеметрии в Application Insights

Каналы телеметрии являются неотъемлемой частью [пакеты SDK Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Она управляет буферизацией и передачей данных телеметрии в службу Application Insights. Версии пакетов SDK для .NET и .NET Core могут использовать два канала встроенная функция телеметрии: `InMemoryChannel` и `ServerTelemetryChannel`. В этой статье описывается каждый канал, в том числе Настройка поведения канала.

## <a name="what-are-telemetry-channels"></a>Что представляют собой каналы телеметрии?

Данные телеметрии каналы отвечают за буферизации элементы телеметрии и отправлять их в службу Application Insights, где они хранятся для выполнения запросов и анализа. Канал телеметрии представляет собой любой класс, реализующий [ `Microsoft.ApplicationInsights.ITelemetryChannel` ](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) интерфейс.

`Send(ITelemetry item)` Канала телеметрии был вызван после всех инициализаторы телеметрии и вызываются обработчики данных телеметрии. Таким образом любые элементы, отброшенные обработчика данных телеметрии не достигают канала. `Send()` не отправляет обычно элементы к серверной части мгновенно. Как правило он помещает их в буфер в памяти и отправляет их в пакетах, для эффективной передачи данных.

[Live Stream метрики](live-stream.md) также имеет пользовательского канала, лежащих в основе динамической потоковой передачи данных телеметрии. Этот канал не зависит от канал регулярных телеметрии, и в этом документе не применяется к нему.

## <a name="built-in-telemetry-channels"></a>Встроенная функция телеметрии каналов

В Application Insights .NET и пакетов SDK для .NET Core поставляются с помощью двух встроенных каналов:

* `InMemoryChannel`: Упрощенный канал, который помещает элементы в памяти, пока они отправляются. Элементы помещаются в буфер в памяти и записаны на диск каждые 30 секунд или каждый раз, когда 500 элементов помещаются в буфер. Этот канал обеспечивает гарантии надежности минимальным, поскольку отправлять данные телеметрии не осуществляет повторную попытку после сбоя. Этот канал также не оставлять элементы на диске, поэтому любые неотправленные теряются без возможности восстановления после завершения работы приложения (нормальное или нет). Этот канал реализует `Flush()` метод, который можно использовать для принудительного удаления все элементы телеметрии в памяти синхронно. Этот канал идеально подходит для краткосрочных приложений которых синхронный flush является идеальным.

    Этот канал является частью большего размера пакета Microsoft.ApplicationInsights NuGet и — канал по умолчанию, в пакете SDK используется при настройке ничего.

* `ServerTelemetryChannel`: Более сложные канал, который имеет политики повтора и возможности для хранения данных на локальном диске. Этот канал повторяет отправку данных телеметрии при возникновении временных ошибок. Этот канал также использует локальное дисковое хранилище для хранения элементов на диске во время отключения сети или объемом данных телеметрии высокого уровня. Из-за эти механизмы повторных попыток и локальное дисковое хранилище этот канал считается более надежным и рекомендуется для всех рабочих сценариев. Этот канал используется по умолчанию для [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) и [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) приложения, которые настроены в соответствии с официальной документации. Этот канал оптимизирована для сценариев сервера с длительно выполняемых процессов. [ `Flush()` ](#which-channel-should-i-use) Не синхронный метод, который реализуется этот канал.

    Этот канал поставляется как пакет Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet и получена автоматически при использовании Microsoft.ApplicationInsights.AspNetCore NuGet или Microsoft.ApplicationInsights.Web пакет.

## <a name="configure-a-telemetry-channel"></a>Настройте канал телеметрии

Настройте канал телеметрии при установке для него конфигурацию active телеметрии. Для приложений ASP.NET, конфигурация включает в себя установку экземпляра канала телеметрии `TelemetryConfiguration.Active`, или путем изменения `ApplicationInsights.config`. Для приложений ASP.NET Core конфигурация включает в себя добавление канала контейнера внедрения зависимостей.

В следующих разделах показаны примеры настройки `StorageFolder` для канала в различных типов приложений. `StorageFolder` является только одним из настраиваемых параметров. Полный список параметров конфигурации, см. в разделе [разделе "Параметры"](telemetry-channels.md#configurable-settings-in-channels) далее в этой статье.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Настройка с помощью файла ApplicationInsights.config для приложений ASP.NET

В следующем разделе из [ApplicationInsights.config](configuration-with-applicationinsights-config.md) показывает `ServerTelemetryChannel` каналу, настроенному с `StorageFolder` присвоено пользовательское расположение:

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!-- Telemetry processors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

### <a name="configuration-in-code-for-aspnet-applications"></a>Конфигурация в коде для приложений ASP.NET

Следующий код устанавливает экземпляр «ServerTelemetryChannel» с `StorageFolder` присвоено пользовательское расположение. Добавьте следующий код в начале приложения, как правило, в `Application_Start()` метод в Global.aspx.cs.

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

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Конфигурация в коде для приложений ASP.NET Core

Изменить `ConfigureServices` метод `Startup.cs` класса, как показано ниже:

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

> [!IMPORTANT]
> Настройка канала с помощью `TelemetryConfiguration.Active` не рекомендуется для приложений ASP.NET Core.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Конфигурация в коде для.NET/.NET Core консольных приложений

Для консольных приложений код является одинаковым для .NET и .NET Core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Операционные сведения о ServerTelemetryChannel

`ServerTelemetryChannel` магазины, поступающих элементов в буфере в памяти. Сериализуются, сжимаются и сохраняются в элементы `Transmission` экземпляра каждые 30 секунд, или были буферизировать при 500 элементов. Один `Transmission` экземпляр содержит более 500 элементов и представляет собой пакет данных телеметрии, отправленных за один вызов HTTPS в службу Application Insights.

По умолчанию, равное 10 `Transmission` экземпляры могут отправляться в параллельном режиме. Если данные телеметрии, поступающих более высоким скоростям, или в сети или Application Insights резервного окончания медленно, `Transmission` экземпляры хранятся в памяти. Емкость по умолчанию этот in-Memory `Transmission` буфера составляет 5 МБ. При превышении емкости в памяти, `Transmission` экземпляры хранятся на локальном диске, но не более 50 МБ. `Transmission` экземпляры хранятся на локальном диске также при наличии проблем с сетью. Только элементы, которые хранятся на локальном диске избежать простоев в случае сбоя приложения. Они отправляются при каждом запуске приложения.

## <a name="configurable-settings-in-channels"></a>Настраиваемые параметры в каналах

Полный список настраиваемых параметров для каждого канала см. в разделе:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Ниже приведены наиболее часто используемые параметры для `ServerTelemetryChannel`:

1. `MaxTransmissionBufferCapacity`: Максимальный объем памяти в байтах, используемый каналом для буферизации операций обмена данными в памяти. При достижении этой емкости, новые элементы хранятся непосредственно на локальный диск. Значение по умолчанию — 5 МБ. Установка более высокое значение приводит к менее на диске, но помните, что элементы в памяти, будут потеряны при сбое приложения.

1. `MaxTransmissionSenderCapacity`: Максимальное число `Transmission` экземпляров, которые будут отправляться в Application Insights, в то же время. Значение по умолчанию — 10. Этот параметр можно настроить более высокое значение, что рекомендуется при формировании огромный объем данных телеметрии. Большое обычно возникает в ходе нагрузочного тестирования, или когда выборки выключена.

1. `StorageFolder`: Каталог, который будет использоваться каналом для хранения элементов на диск при необходимости. В Windows Если другие путь не указан явно используется % LOCALAPPDATA % или % TEMP %. В средах, отличных от Windows необходимо указать допустимое расположение или данные телеметрии не будут храниться на локальном диске.

## <a name="which-channel-should-i-use"></a>Какой канал следует использовать?

`ServerTelemetryChannel` рекомендуется для большинства рабочих сценариев, включающих долго выполняющегося приложения. `Flush()` Реализованный метод `ServerTelemetryChannel` не синхронным, и он также не гарантирует отправку все ожидающие пункты из памяти или диска. Если вы используете этот канал в сценариях, где приложение собирается завершить работу, мы рекомендуем, что некоторая задержка вводится после вызова метода `Flush()`. Точный объем задержки, может потребоваться не прогнозируемого. Это зависит от факторов, таких как количество элементов или `Transmission` экземпляры находятся в памяти, сколько имеется на диске, сколько уже получен к серверной части и ли канал находится в середине сценарии экспоненциальной отсрочки.

Если вам нужно выполнять синхронные flush, мы рекомендуем использовать `InMemoryChannel`.

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>В Application Insights канал гарантирует доставку телеметрии? Если нет, сценарии, в которых могут быть потеряны данные телеметрии?

Короткий ответ — то, что ни один из встроенных каналов гарантируют типа транзакций доставки телеметрии к серверной части. `ServerTelemetryChannel` является более сложные по сравнению с `InMemoryChannel` надежной доставки, но также делает только наиболее безопасным местом для отправки данных телеметрии. Данные телеметрии по-прежнему может быть потеряна в нескольких ситуациях, включая следующие распространенные задачи:

1. Элементы в памяти теряются при сбое приложения.

1. При продолжительных периодов неполадки в сети теряются данные телеметрии. Данные телеметрии хранятся на локальном диске во время отключения сети или при возникновении проблем с серверной Application Insights. Тем не менее элементы, созданные более 24 часов, удаляются.

1. Расположения диска по умолчанию для хранения данных телеметрии в Windows: % LOCALAPPDATA % или % TEMP %. Эти места находятся на обычно локального компьютера. Если приложение физически миграции из одного расположения в другой, данные телеметрии, хранящиеся в исходном расположении будет потеряно.

1. В веб-приложений в Windows место хранения диска по умолчанию является D:\local\LocalAppData. Это расположение не сохраняются. Ее удаление приведет к в приложение перезапускается, масштаб схемы и другие подобные операции, что приводит к потере данные телеметрии, хранящиеся в ней. Можно переопределить значение по умолчанию и указать хранилище материализованных расположении, например D:\home. Тем не менее таких материализованных мест обслуживаются удаленного хранилища и таким образом, может выполняться медленно.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>ServerTelemetryChannel работает в системах, отличных от Windows?

Несмотря на то, что имя пакета и пространство имен включает в себя «WindowsServer», этот канал поддерживается в системах, отличных от Windows, со следующим исключением. На системах, отличных от Windows канал не создает папку локального хранилища по умолчанию. Необходимо создать папку локального хранилища и настроить канал для его использования. После настройки локального хранилища, канал работает одинаково во всех системах.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>Создает ли пакет SDK временное локальное хранилище? Данные шифруются в хранилище?

Пакет SDK хранит элементы телеметрии в локальном хранилище, во время проблем с сетью или во время регулирования. Эти данные не шифруется локально.

Для систем Windows пакет SDK автоматически создает временную папку локального в каталог % LOCALAPPDATA % или % TEMP % и ограничивает доступ для администраторов и только для текущего пользователя.

Для систем, отличных от Windows не локальное хранилище создается автоматически с помощью пакета SDK, и поэтому данные не хранятся по умолчанию. Можно самостоятельно создать каталог хранилища и настроить канал для его использования. В этом случае вы отвечаете за то, что каталог защищен.
Дополнительные сведения о [защиты данных и конфиденциальности](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>Пакет SDK с открытым исходным кодом
Как и в каждом пакете SDK для Application Insights каналы являются открытым исходным кодом. Читать и оставлять в код или сообщить о проблеме в [официальный репозиторий GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Дальнейшие действия

* [Выборка](../../azure-monitor/app/sampling.md)
* [Устранение неполадок пакета SDK](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
