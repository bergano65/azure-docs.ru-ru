---
title: Корреляция данных телеметрии Azure Application Insights | Документация Майкрософт
description: Корреляция данных телеметрии Application Insights
services: application-insights
documentationcenter: .net
author: lgayhardt
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/31/2018
ms.reviewer: sergkanz
ms.author: lagayhar
ms.openlocfilehash: b61163f7e2bc4cf4e7029c9852e5baad431fa0e0
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615846"
---
# <a name="telemetry-correlation-in-application-insights"></a>Корреляция данных телеметрии в Application Insights

В мире микрослужб для каждой логической операции требуется выполнение действий в различных компонентах службы. Служба [Application Insights](app-insights-overview.md) может отдельно отслеживать каждый из этих компонентов. Компонент веб-приложения взаимодействует с компонентом поставщика проверки подлинности для проверки учетных данных пользователя и компонентом API для получения данных для визуализации. Компонент API в свою очередь может запрашивать данные из других служб и использовать компоненты поставщика кэша, а также уведомлять компонент выставления счетов об этом вызове. Application Insights поддерживает распределенную корреляцию данных телеметрии. Это позволяет определить, какой компонент отвечает за сбой или снижение производительности.

В этой статье описывается модель данных, используемая службой Application Insights для корреляции данных телеметрии, отправляемых несколькими компонентами. Рассматриваются методы и протоколы распространения контекста. Здесь также рассматривается реализация принципов корреляции на разных языках и платформах.

## <a name="telemetry-correlation-data-model"></a>Модель корреляции данных телеметрии

Application Insights определяет [модель данных](application-insights-data-model.md) для распределенной корреляции данных телеметрии. Для связывания данных телеметрии с логической операцией у каждого элемента телеметрии есть поле контекста `operation_Id`. Этот идентификатор совместно используется каждым элементом телеметрии в распределенной трассировке. Поэтому даже в случае потери телеметрии из одного уровня вы по-прежнему можете связывать данные телеметрии, сообщаемые другими компонентами.

Распределенная логическая операция обычно состоит из набора меньших операций — запросов, обрабатываемых одним из компонентов. Эти операции определяются [телеметрией запросов](application-insights-data-model-request-telemetry.md). Каждый элемент телеметрии запросов имеет собственный уникальный `id`, который его глобально идентифицирует. И для всех данных телеметрии (трассировок, исключений и др.), связанных с этим запросом, следует задать значение `operation_parentId` для `id` запроса.

Каждая исходящая операция, такая как вызов HTTP к другому компоненту, представлена [телеметрией зависимостей](application-insights-data-model-dependency-telemetry.md). Телеметрия зависимостей также определяет собственный `id`, который является глобально уникальным. Телеметрия запросов, инициированная этим вызовом зависимостей, использует его в качестве `operation_parentId`.

Можно создать представление распределенной логической операции, используя `operation_Id`, `operation_parentId` и `request.id` с `dependency.id`. Эти поля также определяют причинно-следственную связь вызовов телеметрии.

В среде микрослужб трассировки компонентов могут отправляться в разные хранилища. Каждый компонент может иметь свой собственный ключ инструментирования в Application Insights. Чтобы получить данные телеметрии для логической операции, необходимо запросить данные из каждого хранилища. Если число хранилищ очень велико, то необходимо дать указание, где продолжить поиск.

Модель данных Application Insights определяет два поля (`request.source` и `dependency.target`) для решения этой проблемы. Первое поле определяет компонент, который инициировал запрос зависимости, а второе — компонент, который вернул ответ вызова зависимостей.


## <a name="example"></a>Пример

Давайте рассмотрим пример приложения STOCK PRICES (Стоимость акций), в котором показывается текущая рыночная стоимость акций. При этом используется внешний интерфейс API, называемый STOCKS API (API акций). В приложении STOCK PRICES есть страница `Stock page`, которая открывается в браузере клиента с помощью `GET /Home/Stock`. Приложение запрашивает STOCK API с помощью HTTP-вызова `GET /api/stock/value`.

Вы можете проанализировать итоговые данные телеметрии, выполнив запрос:

```
(requests | union dependencies | union pageViews) 
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Обратите внимание, что в представлении результата все элементы телеметрии используют корневой `operation_Id`. При вызове AJAX, осуществляемом со страницы, телеметрии зависимостей назначается новый уникальный идентификатор `qJSXU`, а в качестве `operation_ParentId` используется идентификатор pageView. В свою очередь запрос сервера использует идентификатор AJAX в качестве `operation_ParentId`, и т. д.

| itemType   | name                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock page                |              | STYz               | STYz         |
| dependency | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| запрос    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependency | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Теперь, когда к внешней службе осуществлен вызов `GET /api/stock/value`, необходимо узнать идентификатор этого сервера. Поэтому можно соответствующим образом задать поле `dependency.target`. Если внешняя служба не поддерживает мониторинг, то в качестве `target` задается имя узла службы, например `stock-prices-api.com`. Однако если для идентификации этой службы возвращается предопределенный заголовок HTTP, то `target` содержит удостоверение службы, которое позволяет Application Insights создать распределенную трассировку, запросив телеметрию из этой службы. 

## <a name="correlation-headers"></a>Заголовки корреляции

Мы работаем над созданием предложения RFC для [протокола HTTP корреляции](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Это предложение определяет два заголовка:

- `Request-Id` содержит глобальный уникальный идентификатор вызова.
- `Correlation-Context` содержит коллекцию пар "имя-значение" свойств распределенной трассировки.

Стандарт также определяет две схемы создания `Request-Id` — неструктурированная и иерархическая. В неструктурированной схеме для коллекции `Correlation-Context` определяется хорошо известный ключ `Id`.

Служба Application Insights определяет [расширение](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) для протокола HTTP корреляции. Она использует пары "имя-значение" `Request-Context` для распространения коллекции свойств, используемых непосредственным вызывающим или вызываемым. Пакет SDK для Application Insights использует этот заголовок, чтобы задать значения полей `dependency.target` и `request.source`.

### <a name="w3c-distributed-tracing"></a>Распределенная трассировка W3C

Мы переходим на [формат распределенной трассировки W3C](https://w3c.github.io/trace-context/). Он определяет следующее:
- `traceparent` — содержит глобальный уникальный идентификатор операции и уникальный идентификатор вызова;
- `tracestate` — содержит специфический контекст трассировки системы.

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-classic-apps"></a>Включение поддержки распределенной трассировки W3C для приложений ASP.NET Classic

Эта функция доступна в пакетах Microsoft.ApplicationInsights.Web и Microsoft.ApplicationInsights.DependencyCollector, начиная с версии 2.8.0-beta1.
Она **отключена** по умолчанию; чтобы включить ее, измените `ApplicationInsights.config`:

* в разделе `RequestTrackingTelemetryModule` добавьте `EnableW3CHeadersExtraction` элемент со значением `true`;
* в разделе `DependencyTrackingTelemetryModule` добавьте `EnableW3CHeadersInjection` элемент со значением `true`.

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Включение поддержки распределенной трассировки W3C для приложений ASP.NET Core

Эта функция доступна в пакетах Microsoft.ApplicationInsights.AspNetCore версии 2.5.0-beta1 и Microsoft.ApplicationInsights.DependencyCollector версии 2.8.0-beta1.
Она **отключена** по умолчанию; чтобы включить ее, задайте `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` как `true`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

## <a name="open-tracing-and-application-insights"></a>OpenTracing и Application Insights

[Спецификации модели данных OpenTracing ](http://opentracing.io/) и модели данных Application Insights сопоставлены следующим образом.

| Application Insights                  | OpenTracing                                      |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` с `span.kind = server`                  |
| `Dependency`                          | `Span` с `span.kind = client`                  |
| `Id` из `Request` и `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` типа `ChildOf` (родительский диапазон)   |

Дополнительные сведения см. в статье [Модель данных телеметрии Application Insights](application-insights-data-model.md). 

Определения основных понятий OpenTracing см. в [спецификации](https://github.com/opentracing/specification/blob/master/specification.md) и [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).


## <a name="telemetry-correlation-in-net"></a>Корреляция данных телеметрии в .NET

Со временем в .NET было определено несколько способов корреляции данных телеметрии и журналов диагностики. `System.Diagnostics.CorrelationManager` позволяет отслеживать свойства [LogicalOperationStack и ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). `System.Diagnostics.Tracing.EventSource` и трассировка событий Windows определяют метод [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx). `ILogger` использует [области журналов](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). WCF и HTTP подключают распространение "текущего" контекста.

Однако эти методы не обеспечивали поддержку автоматической распределенной трассировки. `DiagnosticsSource` — это способ обеспечить поддержку автоматической корреляции между компьютерами. Библиотеки .NET поддерживают DiagnosticsSource и разрешают автоматическое распространение контекста корреляции между компьютерами, используя транспортный протокол, например HTTP.

В [руководстве пользователя по классу Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) представлены общие сведения об отслеживании действий в DiagnosticsSource. 

ASP.NET Core 2.0 поддерживает извлечение заголовков HTTP и запуск нового действия (Activity). 

`System.Net.HttpClient`, начиная с версии `4.1.0`, поддерживает автоматическое внедрение заголовков HTTP корреляции и отслеживание вызова HTTP как действия.

Для ASP.NET Classic доступен новый HTTP-модуль [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/). Этот модуль реализует корреляцию данных телеметрии с помощью DiagnosticsSource. Он запускает действие, исходя их заголовков входящего запроса. Он также сопоставляет данные телеметрии из разных этапов обработки запроса. Даже если каждый этап обработки IIS выполняется в разных потоках управления.

Пакет SDK для Application Insights, начиная с версии `2.4.0-beta1`, использует DiagnosticsSource и Activity для сбора данных телеметрии и их связывания с текущим действием. 

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Корреляция данных телеметрии в пакете SDK для Java
[Пакет SDK для Java Application Insights](app-insights-java-get-started.md) поддерживает автоматическую корреляцию данных телеметрии, начиная с версии `2.0.0`. Он автоматически заполняет `operation_id` для всех данных телеметрии (трассировки, исключения, пользовательские события и т. д), генерируемых в рамках запроса. Он также распространяет заголовки корреляции (описаны выше) для вызовов между службами по протоколу HTTP при наличии настроенного [агента Java SDK](app-insights-java-agent.md). Примечание. Функция корреляции поддерживает только вызовы, выполненные через HTTP-клиент Apache. Если применяется шаблон Spring Rest или Feign, они оба могут использоваться с HTTP-клиентом Apache в неявном виде.

В настоящее время автоматическое распространение контекстной информации не поддерживается технологиями обмена сообщениями (например, Kafka, RabbitMQ, служебная шина Azure). Однако можно вручную написать код для таких сценариев, используя API `trackDependency` и `trackRequest`. При этом телеметрия зависимостей представляет сообщение, поставленное в очередь поставщиком, а запрос представляет сообщение, обрабатываемое пользователем. В этом случае и `operation_id`, и `operation_parentId` должны быть указаны в свойствах сообщений.

<a name="java-role-name"></a>
### <a name="role-name"></a>Имя роли
В некоторых случаях может потребоваться настроить способ отображения названий компонентов в [схеме приложений](app-insights-app-map.md). Чтобы сделать это, можно вручную задать `cloud_roleName` одним из следующих способов.

Если вы используете `WebRequestTrackingFilter`, `WebAppNameContextInitializer` автоматически задаст имя приложения. Добавьте следующее в файл конфигурации (ApplicationInsights.xml):
```XML
<ContextInitializers>
  <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
</ContextInitializers>
```
С использованием класса контекста облака:
```Java
telemetryClient.getContext().getCloud().setRole("My Component Name");
```

## <a name="next-steps"></a>Дополнительная информация

- [API Application Insights для пользовательских событий и метрик](app-insights-api-custom-events-metrics.md)
- Подключите все компоненты своей микрослужбы с помощью Application Insights. Ознакомьтесь со сведениями о [поддерживаемых платформах](app-insights-platforms.md).
- В [этой статье](application-insights-data-model.md) представлены типы данных и модель данных для Application Insights.
- Вы можете узнать, как [расширять и фильтровать данные телеметрии](app-insights-api-filtering-sampling.md).
- [Справочник по конфигурации в Application Insights](app-insights-configuration-with-applicationinsights-config.md)

