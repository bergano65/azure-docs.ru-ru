---
title: Диагностика с помощью Live Metrics Stream Azure Application Insights
description: Мониторинг веб-приложения в реальном времени с помощью пользовательских метрик и диагностика проблем с помощью динамического веб-канала сбоев, трассировок и событий.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: ac2aabe12697336377df808e02e283dde0e4da16
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927226"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: мониторинг и диагностика с задержкой в 1 секунду

Держите руку на пульсе работы веб-приложения с помощью Live Metrics Stream в [Application Insights](../../azure-monitor/app/app-insights-overview.md). Выбирайте и фильтруйте метрики и счетчики производительности для отслеживания в режиме реального времени, не нарушая работу служб. Проверяйте трассировки стека на основе образцов неудавшихся запросов и исключений. Вместе с [профилировщиком](../../azure-monitor/app/profiler.md)— [отладчик моментальных снимков](../../azure-monitor/app/snapshot-debugger.md). Live Metrics Stream предоставляет мощный и неагрессивный диагностический инструмент для веб-сайта.

С помощью Live Metrics Stream можно выполнять следующие действия:

* Проверять выпущенное исправление, наблюдая за производительностью и числом сбоев.
* Наблюдать за результатом применения тестовых нагрузок и диагностировать проблемы в режиме реального времени.
* Сосредотачиваться на определенных тестовых сеансах или отфильтровывать известные проблемы, выбирая и фильтруя метрики, подлежащие отслеживанию.
* Получать трассировки исключений по мере того, как они возникают.
* Экспериментировать с фильтрами, чтобы найти наиболее важные ключевые показатели эффективности.
* Отслеживать любые счетчики производительности Windows в режиме реального времени.
* Легко определить сервер, на котором возникают проблемы, и с помощью фильтра получить все КПЭ и динамические веб-каналы, относящиеся только к этому серверу.

[![Видео, посвященное Live Metrics Stream](./media/live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

В настоящее время динамические метрики поддерживаются для ASP.NET, ASP.NET Core, функций Azure, Java и приложений Node. js.

## <a name="get-started"></a>Начать

1. Если вы еще не [установили Application Insights в своем веб-приложении](../../azure-monitor/azure-monitor-app-hub.md), сделайте это сейчас.
2. В дополнение к стандартным пакетам Application Insights пакет [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) также требуется, чтобы включить Live Metrics Stream.
3. **Выполните обновление до последней версии** пакета Application Insights. В Visual Studio щелкните проект правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**. Откройте вкладку **Обновления** и выберите все пакеты "Microsoft.ApplicationInsights.*".

    Разверните приложение заново.

3. На [портале Azure](https://portal.azure.com) откройте ресурс Application Insights для своего приложения, а затем откройте Live Stream.

4. [Защитите канал управления](#secure-the-control-channel), если в фильтрах могут использоваться конфиденциальные данные, например имена клиентов.

### <a name="no-data-check-your-server-firewall"></a>Нет данных? Проверьте брандмауэр сервера

Убедитесь в том, что [исходящие порты для Live Metrics Stream](../../azure-monitor/app/ip-addresses.md#outgoing-ports) открыты в брандмауэре ваших серверов.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Чем Live Metrics Stream отличается от обозревателя метрик и службы аналитики?

| |Live Stream | Обозреватель метрик и служба аналитики |
|---|---|---|
|Задержка|Данные отображаются в течение одной секунды|Агрегирование выполняется в течение нескольких минут|
|Нет сохранения|Данные сохраняются, только пока они отображаются на диаграмме, а затем удаляются.|[Данные сохраняются 90 дней](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|По запросу|Данные передаются, пока открыта служба Live Metrics|Данные отправляются, когда пакет SDK установлен и включен|
|Бесплатные|Плата за данные Live Stream не взимается|Действуют [расценки](../../azure-monitor/app/pricing.md)
|Выборка|Передаются все выбранные метрики и счетчики. Производится выборка сбоев и трассировок стека. TelemetryProcessors не применяются.|Может производиться [выборка](../../azure-monitor/app/api-filtering-sampling.md) событий.|
|Канал управления|В пакет SDK отправляются управляющие сигналы фильтрации. Мы советуем защитить этот канал.|Обмен данными — это один из способов, на портале|

## <a name="select-and-filter-your-metrics"></a>Выбор и фильтрация метрик

(Доступно с ASP.NET, ASP.NET Core и Функциями Azure версии 2.)

Можно отслеживать пользовательский КПЭ в реальном времени, применяя произвольные фильтры к любым данным телеметрии Application Insights на портале. Щелкните элемент управления фильтром, появляющийся при наведении указателя мыши на любую диаграмму. На диаграмме ниже показан пользовательский КПЭ числа запросов с фильтрами по для атрибутам URL-адреса и длительности. Проверьте результат фильтрации в разделе "Просмотр потока", в котором отображается динамический веб-канал телеметрии, соответствующей критериям, которые можно указать в любой момент времени.

![Пользовательский КПЭ запросов](./media/live-stream/live-stream-filteredMetric.png)

Вы можете отслеживать не только количество. Параметры зависят от типа потока. Он может передавать любые данные телеметрии Application Insights: запросы, зависимости, исключения, трассировки, события или метрики. Поток может передавать и ваше [пользовательское измерение](../../azure-monitor/app/api-custom-events-metrics.md#properties).

![Параметры значения](./media/live-stream/live-stream-valueoptions.png)

Помимо телеметрии Application Insights, можно также отслеживать любой счетчик производительности Windows, выбрав его в параметрах потока и указав имя счетчика производительности.

Динамические метрики объединяются дважды: локально на каждом сервере, а затем на всех серверах. Можно изменить режим по умолчанию для обоих случаев, выбрав другие параметры в соответствующих раскрывающихся списках.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Пример данных телеметрии: пользовательские события динамической диагностики
По умолчанию динамический веб-канал событий отображает примеры завершившихся сбоем запросов и вызовов зависимостей, исключений, событий и трассировок. Щелкните значок фильтра, чтобы просмотреть действующие критерии в любой момент времени. 

![Динамический веб-канал по умолчанию](./media/live-stream/live-stream-eventsdefault.png)

Как и для метрик, можно указать любые произвольные критерии для любого типа данных телеметрии Application Insights. В этом примере мы выбираем конкретные сбои запросов, трассировки и события. Мы также выбираем все исключения и сбои зависимостей.

![Пользовательский динамический веб-канал](./media/live-stream/live-stream-events.png)

Примечание. В настоящее время для критериев на основе сообщений об исключениях используйте сообщение о внешнем исключении. В предыдущем примере, чтобы отфильтровать неопасные исключения с сообщением о внутреннем исключении (после разделителя "<--") "Клиент отключен", использовался критерий "Message not-contains "Error reading request content"" (Сообщение не содержит "Ошибка при чтении содержимого запроса").

Просмотрите сведения об элементе динамического веб-канала, щелкнув его. Можно приостановить веб-канал, щелкнув **Приостановить**, прокрутив вниз или щелкнув элемент. Работа динамического веб-канала возобновится, когда вы прокрутите его обратно до начала или щелкните счетчик элементов, собранных во время приостановки.

![Выборка динамических ошибок](./media/live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Фильтрация по экземпляру сервера

Если требуется отслеживать определенный экземпляр роли сервера, можно применить фильтрацию по серверу.

![Выборка динамических ошибок](./media/live-stream/live-stream-filter.png)

## <a name="secure-the-control-channel"></a>Защита канала управления
Указываемые вами пользовательские критерии фильтра передаются в компонент Live Metrics в пакете SDK для Application Insights. Фильтры могут содержать конфиденциальные сведения, например идентификаторы клиентов. Помимо ключа инструментирования, канал для их передачи можно защитить секретным ключом API.
### <a name="create-an-api-key"></a>Создание ключа API

![Создание ключа API](./media/live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Добавление ключа API в конфигурацию

### <a name="classic-aspnet"></a>Классический ASP.NET

В файле applicationinsights.config добавьте AuthenticationApiKey в QuickPulseTelemetryModule.
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Или задайте его в коде для QuickPulseTelemetryModule.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Extensibility;

             TelemetryConfiguration configuration = new TelemetryConfiguration();
            configuration.InstrumentationKey = "YOUR-IKEY-HERE";

            QuickPulseTelemetryProcessor processor = null;

            configuration.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    processor = new QuickPulseTelemetryProcessor(next);
                    return processor;
                })
                        .Build();

            var QuickPulse = new QuickPulseTelemetryModule()
            {

                AuthenticationApiKey = "YOUR-API-KEY"
            };
            QuickPulse.Initialize(configuration);
            QuickPulse.RegisterTelemetryProcessor(processor);
            foreach (var telemetryProcessor in configuration.TelemetryProcessors)
                {
                if (telemetryProcessor is ITelemetryModule telemetryModule)
                    {
                    telemetryModule.Initialize(configuration);
                    }
                }

```

### <a name="azure-function-apps"></a>Приложения-функции Azure

Для приложений-функций Azure (v2) Защита канала с помощью ключа API может быть выполнена с помощью переменной среды.

Создайте ключ API из ресурса Application Insights и перейдите к **параметрам приложения** вашего приложения-функции. Выберите **Добавить новый параметр** и введите имя `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` и значение, соответствующее ключу API.

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-beta-or-greater"></a>ASP.NET Core (требует пакет SDK для ASP.NET Core для Application Insights версии 2.3.0-beta или выше)

Измените файл startup.cs следующим образом.

Сначала добавьте приведенный ниже код.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Затем в методе ConfigureServices добавьте:

```csharp
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Однако, если все подключенные серверы опознаны и надежны, можно использовать настраиваемые фильтры без аутентифицированного канала. Эта возможность доступна в течение шести месяцев. Это переопределение требуется после каждого создания сеанса или подключения нового сервера.

![Параметры аутентификации Live Metrics](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>Настоятельно рекомендуется установить аутентифицированный канал перед вводом в критерии фильтра потенциально конфиденциальной информации, например идентификатора клиента.
>

## <a name="supported-features-table"></a>Таблица поддерживаемых возможностей

| Язык                         | Основные метрики       | Метрики производительности | Настраиваемая фильтрация    | Пример телеметрии    | Разделение ЦП по процессам |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET                             | Поддерживается (V 2.7.2 +) | Поддерживается (V 2.7.2 +) | Поддерживается (V 2.7.2 +) | Поддерживается (V 2.7.2 +) | Поддерживается (V 2.7.2 +)  |
| .NET Core (целевой объект =. NET Framework)| Поддерживается (V 2.4.1 +) | Поддерживается (V 2.4.1 +) | Поддерживается (V 2.4.1 +) | Поддерживается (V 2.4.1 +) | Поддерживается (V 2.4.1 +)  |
| .NET Core (целевой объект =. NET Core)     | Поддерживается (V 2.4.1 +) | Поддерживается*          | Поддерживается (V 2.4.1 +) | Поддерживается (V 2.4.1 +) | **Не поддерживается**    |
| Функции Azure v2               | Поддерживается           | Поддерживается           | Поддерживается           | Поддерживается           | **Не поддерживается**    |
| Java:                             | Поддерживается (V 2.0.0 +) | Поддерживается (V 2.0.0 +) | **Не поддерживается**   | **Не поддерживается**   | **Не поддерживается**    |
| Node.js                          | Поддерживается (V 1.3.0 +) | Поддерживается (V 1.3.0 +) | **Не поддерживается**   | Поддерживается (V 1.3.0 +) | **Не поддерживается**    |

Основные метрики включают в себя запрос, зависимость и частоту исключений. Метрики производительности (счетчики производительности) включают в себя память и ЦП. Пример телеметрии показывает поток подробных сведений о неудачных запросах и зависимостях, исключениях, событиях и трассировках.

 \* поддержка PerfCounters незначительно различается в разных версиях .NET Core, которые не предназначены для .NET Framework.

- Метрики PerfCounters поддерживаются при работе в службе приложений Azure для Windows. (Пакет SDK для AspNetCore версии 2.4.1 или более поздней)
- PerfCounters поддерживаются, когда приложение выполняется на любых компьютерах Windows (виртуальная машина или облачная служба или локальная и т. д.). (Пакет SDK для AspNetCore версии 2.7.1 или более поздней), но для приложений, предназначенных для .NET Core 2,0 или более поздних версий.
- PerfCounters поддерживаются, когда приложение работает где УГОДно (Linux, Windows, служба приложений для Linux, контейнеры и т. д.) в последней бета-версии (т. е. AspNetCore SDK версии 2.8.0-beta1 или более поздней, но для приложений, предназначенных для .NET Core 2,0 или более поздних версий.

По умолчанию динамические метрики отключены в пакете SDK для Node. js. Чтобы включить динамические метрики, добавьте `setSendLiveMetrics(true)` к [методам конфигурации](https://github.com/Microsoft/ApplicationInsights-node.js#configuration) при инициализации пакета SDK.

## <a name="troubleshooting"></a>Устранение неисправностей

Нет данных? Если приложение находится в защищенной сети: Live Metrics Stream использует IP-адреса, отличные от других Application Insights телеметрии. Убедитесь, что [эти IP-адреса](../../azure-monitor/app/ip-addresses.md) открыты в брандмауэре.

## <a name="next-steps"></a>Дальнейшие действия
* [Отслеживание использования Application Insights.](../../azure-monitor/app/usage-overview.md)
* [Использование диагностического поиска](../../azure-monitor/app/diagnostic-search.md)
* [Профилировщик](../../azure-monitor/app/profiler.md)
* [Отладчик моментальных снимков](../../azure-monitor/app/snapshot-debugger.md)
