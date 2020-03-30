---
title: Телеметрические каналы в Azure Application Insights Документы Майкрософт
description: Как настроить каналы телеметрии в SDK Azure Application Insights для .NET и .NET Core.
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 9c292246f947e4d3a364f79b31fe7a1deebd33d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275702"
---
# <a name="telemetry-channels-in-application-insights"></a>Телеметрические каналы в application Insights

Каналы телеметрии являются неотъемлемой частью [SDK Azure Application Insights.](../../azure-monitor/app/app-insights-overview.md) Они управляют буферизацией и передачей телеметрии в службу Application Insights. Версии SDK .NET и .NET Core имеют два встроенных `InMemoryChannel` `ServerTelemetryChannel`телеметрических канала: и . В этой статье подробно описан каждый канал, в том числе как настроить поведение канала.

## <a name="what-are-telemetry-channels"></a>Что такое телеметрические каналы?

Телеметрические каналы отвечают за буферизирование элементов телеметрии и отправку их в службу Application Insights, где они хранятся для запроса и анализа. Телеметрический канал — это любой [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) класс, который реализует интерфейс.

Метод `Send(ITelemetry item)` телеметрического канала называется после того, как все инициали телеметрии и телеметрические процессоры называются. Таким образом, любые элементы, сброшенные телеметрическим процессором, не дойдут до канала. `Send()`обычно не отправляет элементы на задний конец мгновенно. Как правило, он буферизирует их в памяти и отправляет их партиями, для эффективной передачи.

[Live Metrics Stream](live-stream.md) также имеет пользовательский канал, который питает прямую трансляцию телеметрии. Этот канал не зависит от регулярного телеметрического канала, и этот документ не распространяется на него.

## <a name="built-in-telemetry-channels"></a>Встроенные телеметрические каналы

Приложение Insights .NET и .NET Core SDK отправляются с двумя встроенными каналами:

* `InMemoryChannel`: Легкий канал, буферизуя элементы в памяти до тех пор, пока они не будут отправлены. Элементы буферизируются в памяти и промываются один раз в 30 секунд, или всякий раз, когда 500 элементов буферизированы. Этот канал предлагает минимальные гарантии надежности, потому что он не повторно отправки телеметрии после сбоя. Этот канал также не хранит элементы на диске, поэтому любые неотправленные элементы теряются навсегда при выключении приложения (изящные или нет). Этот канал реализует `Flush()` метод, который может быть использован для принудительного флеша любых элементов телеметрии в памяти синхронно. Этот канал хорошо подходит для краткосрочных приложений, где синхронный флеш является идеальным.

    Этот канал является частью более крупного пакета Microsoft.ApplicationInsights NuGet и является каналом по умолчанию, который использует SDK, когда больше ничего не настроено.

* `ServerTelemetryChannel`: Более продвинутый канал, который имеет политики повторной попытки и возможность хранения данных на локальном диске. Этот канал повторно отправляет телеметрию в случае временных ошибок. Этот канал также использует локальное хранилище дисков для хранения элементов на диске во время перебоев в работе сети или больших объемов телеметрии. Из-за этих механизмов повторной попытки и локального хранения диска этот канал считается более надежным и рекомендуется для всех сценариев производства. Этот канал является по умолчанию для [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) и [ASP.NET основных](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) приложений, которые настроены в соответствии с официальной документацией. Этот канал оптимизирован для серверных сценариев с длительными процессами. Метод, [`Flush()`](#which-channel-should-i-use) реализованный этим каналом, не синхронный.

    Этот канал поставляется как пакет Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet и приобретается автоматически при использовании либо Microsoft.ApplicationInsights.Web или Microsoft.ApplicationInsights.AspNetCore NuGet Пакет.

## <a name="configure-a-telemetry-channel"></a>Настройка телеметрического канала

Вы настраиваете телеметрический канал, установив его в активную конфигурацию телеметрии. Для ASP.NET приложений конфигурация включает в себя `TelemetryConfiguration.Active`настройку экземпляра телеметрического канала на или путем `ApplicationInsights.config`изменения. Для ASP.NET основных приложений конфигурация включает в себя добавление канала в контейнер для впрыски зависимостей.

В следующих разделах приведены `StorageFolder` примеры настройки настройки для канала в различных типах приложений. `StorageFolder`является лишь одним из настраиваемых настроек. Полный список настроек конфигурации можно узнать [позже](telemetry-channels.md#configurable-settings-in-channels) в этой статье.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Конфигурация с помощью ApplicationInsights.config для ASP.NET приложений

В следующем разделе от [ApplicationInsights.config](configuration-with-applicationinsights-config.md) `ServerTelemetryChannel` показан `StorageFolder` канал, настроенный с набором на пользовательское местоположение:

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

### <a name="configuration-in-code-for-aspnet-applications"></a>Конфигурация в коде для ASP.NET приложений

Следующий код настраивает экземпляр 'ServerTelemetryChannel' с `StorageFolder` набором в пользовательское местоположение. Добавьте этот код в начале `Application_Start()` приложения, как правило, в методе в Global.aspx.cs.

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

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Конфигурация в коде для ASP.NET основных приложений

Измените `ConfigureServices` метод `Startup.cs` класса, как показано здесь:

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
> Настройка канала с `TelemetryConfiguration.Active` помощью не рекомендуется для ASP.NET приложений Core.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Конфигурация в коде для консольных приложений .NET/.NET Core

Для консольных приложений код одинаков как для .NET, так и для .NET Core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Оперативные детали ServerTelemetryChannel

`ServerTelemetryChannel`магазины, прибывающие в буфере памяти. Элементы сериализуются, сжимаются `Transmission` и хранятся в экземпляре один раз в 30 секунд, или когда 500 элементов были буферизированы. Один `Transmission` экземпляр содержит до 500 элементов и представляет собой партию телеметрии, отправленной по одному вызову HTTPS в службу Application Insights.

По умолчанию параллельно может `Transmission` быть отправлено не более 10 экземпляров. Если телеметрия прибывает с более быстрыми темпами, или если сеть `Transmission` или задняя часть Application Insights происходит медленно, экземпляры сохраняются в памяти. Емкость этого буфера памяти `Transmission` по умолчанию составляет 5 МБ. При превышении емкости памяти `Transmission` экземпляры хранятся на локальном диске до предела 50 МБ. `Transmission`экземпляры хранятся на локальном диске также при наличии проблем с сетью. Только те элементы, которые хранятся на локальном диске, выживают в сбое приложения. Они отправляются всякий раз, когда приложение начинается снова.

## <a name="configurable-settings-in-channels"></a>Настраиваемые настройки в каналах

Полный список настраиваемых настроек для каждого канала можно узнать:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [СерверТелеметрияКанал](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Вот наиболее часто используемые настройки для: `ServerTelemetryChannel`

1. `MaxTransmissionBufferCapacity`: Максимальное количество памяти, в байтах, используемых каналом для буфера передач в памяти. При обнаружении этой емкости новые элементы хранятся непосредственно на локальном диске. Значение по умолчанию — 5 МБ. Установка более высокого значения приводит к меньшему использованию диска, но помните, что элементы в памяти будут потеряны, если приложение выходит из строя.

1. `MaxTransmissionSenderCapacity`: Максимальное `Transmission` количество экземпляров, которые будут отправлены в Application Insights одновременно. Значение по умолчанию — 10. Этот параметр может быть настроен на большее число, которое рекомендуется при создании огромного объема телеметрии. Высокий объем обычно возникает во время тестирования нагрузки или при выключении выборки.

1. `StorageFolder`Папка, используемая каналом для хранения элементов на диске по мере необходимости. В Windows используется либо %LOCALAPPDATA%, либо %TEMP%, если нет конкретного пути. В других средах, помимо Windows, необходимо указать действительное местоположение или телеметрию, не хранящуюся на локальном диске.

## <a name="which-channel-should-i-use"></a>Какой канал я должен использовать?

`ServerTelemetryChannel`рекомендуется для большинства сценариев производства с участием долгосрочных приложений. Метод, `Flush()` реализованный `ServerTelemetryChannel` не синхронным, и он также не гарантирует отправку всех отложенных элементов из памяти или диска. Если вы используете этот канал в сценариях, где приложение вот-вот `Flush()`выключится, мы рекомендуем вам ввести некоторую задержку после вызова. Точная задержка, которая может потребоваться, не предсказуема. Это зависит от таких факторов, как, сколько элементов или `Transmission` экземпляров в памяти, сколько находятся на диске, сколько передаются на задней части, и является ли канал находится в середине экспоненциальных сценариев обратного от.

Если вам нужно сделать синхронный флеш, `InMemoryChannel`мы рекомендуем вам использовать .

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Гарантирует ли канал Application Insights доставку телеметрии? Если нет, то каковы сценарии, в которых телеметрия может быть потеряна?

Краткий ответ заключается в том, что ни один из встроенных каналов не предлагает гарантию передачи телеметрии в задней части. `ServerTelemetryChannel`является более продвинутым по сравнению с `InMemoryChannel` для надежной доставки, но он также делает только лучшие усилия попытка отправить телеметрии. Телеметрия все еще может быть потеряна в нескольких ситуациях, включая следующие общие сценарии:

1. Элементы памяти теряются при сбое приложения.

1. Телеметрия теряется в течение длительных периодов сетевых проблем. Телеметрия хранится на локальном диске во время сбоев в сети или при возникновении проблем с задним концом Application Insights. Тем не менее, элементы старше 48 часов отбрасываются.

1. Местоположение диска по умолчанию для хранения телеметрии в Windows% LOCALAPPDATA% или %TEMP%. Эти местоположения, как правило, являются локальными для машины. Если приложение мигрирует физически из одного места в другое, любая телеметрия, хранящаяся в исходном месте, теряется.

1. В веб-приложениях на Windows место хранения дисков по умолчанию находится d: «Местные» - LocalAppData. Это место не сохраняется. Это уничтожено в перезапусках приложений, масштабных и других подобных операциях, что приводит к потере любой телеметрии, хранящейся там. Можно переопределить значение по умолчанию и указать хранилище в сохраняемом месте, например D:'home. Тем не менее, такие упорные места обслуживаются удаленным хранилищем и поэтому могут быть медленными.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>Работает ли ServerTelemetryChannel на других системах, кроме Windows?

Хотя название его пакета и пространства имен включает в себя "WindowsServer", этот канал поддерживается в других системах, кроме Windows, за следующим исключением. В системах, помимо Windows, канал не создает локальную папку хранения по умолчанию. Необходимо создать локальную папку хранения и настроить канал для ее использования. После настройки локального хранилища канал работает одинаково на всех системах.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>Создает ли пакет SDK временное локальное хранилище? Зашифрованы ли данные при хранении?

SDK хранит телеметрические элементы в локальном хранилище во время сетевых проблем или во время регулирования. Эти данные не зашифрованы локально.

Для систем Windows SDK автоматически создает временную локальную папку в каталоге %TEMP% или %LOCALAPPDATA% и ограничивает доступ только к администраторам и только текущему пользователю.

Для систем, помимо Windows, sDK не создает локальное хранилище автоматически, и поэтому данные по умолчанию не хранятся локально. Вы можете создать каталог хранения самостоятельно и настроить канал, чтобы использовать его. В этом случае вы несете ответственность за обеспечение безопасности каталога.
Узнайте больше о [защите данных и конфиденциальности](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>Пакет SDK с открытым исходным кодом
Как и все SDK для Application Insights, каналы являются открытым исходным кодом. Прочитайте и внесите свой вклад в код или сообщите о проблемах на [официальном репо GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Дальнейшие действия

* [Выборки](../../azure-monitor/app/sampling.md)
* [SDK Устранение неполадок](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
