---
title: Корреляция данных телеметрии Azure Application Insights | Документация Майкрософт
description: Корреляция данных телеметрии Application Insights
services: application-insights
documentationcenter: .net
author: lgayhardt
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/14/2019
ms.reviewer: sergkanz
ms.author: lagayhar
ms.openlocfilehash: d3aad8f1b032960786564bbb18f99c260fd72113
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58092724"
---
# <a name="telemetry-correlation-in-application-insights"></a>Корреляция данных телеметрии в Application Insights

В мире микрослужб для каждой логической операции требуется выполнение действий в различных компонентах службы. Служба [Application Insights](../../azure-monitor/app/app-insights-overview.md) может отдельно отслеживать каждый из этих компонентов. Компонент веб-приложения взаимодействует с компонентом поставщика проверки подлинности для проверки учетных данных пользователя и компонентом API для получения данных для визуализации. Компонент API может запрашивать данные из других служб и использовать компоненты поставщика кэша, чтобы уведомлять компонент выставления счетов об этом вызове. Application Insights поддерживает распределенную корреляцию данных телеметрии, позволяющую определить, какой компонент отвечает за сбой или снижение производительности.

В этой статье описывается модель данных, используемая службой Application Insights для корреляции данных телеметрии, отправляемых несколькими компонентами. Рассматриваются методы и протоколы распространения контекста. Здесь также рассматривается реализация принципов корреляции на разных языках и платформах.

## <a name="data-model-for-telemetry-correlation"></a>Модель корреляции для данных телеметрии

Application Insights определяет [модель данных](../../azure-monitor/app/data-model.md) для распределенной корреляции данных телеметрии. Для связывания данных телеметрии с логической операцией у каждого элемента телеметрии есть поле контекста `operation_Id`. Этот идентификатор совместно используется каждым элементом телеметрии в распределенной трассировке. Поэтому даже в случае потери телеметрии из одного уровня вы по-прежнему можете связывать данные телеметрии, сообщаемые другими компонентами.

Распределенная логическая операция обычно состоит из набора меньших операций, то есть запросов, обрабатываемых одним из компонентов. Эти операции определяются [телеметрией запросов](../../azure-monitor/app/data-model-request-telemetry.md). Каждый элемент телеметрии запросов имеет собственный уникальный `id`, который его глобально идентифицирует. И для всех элементов телеметрии (например, трассировок и исключений), связанных с этим запросом, следует задать значение `operation_parentId` для `id` запроса.

Каждая исходящая операция (например, вызов HTTP к другому компоненту) представлена [телеметрией зависимостей](../../azure-monitor/app/data-model-dependency-telemetry.md). Телеметрия зависимостей также определяет собственный `id`, который является глобально уникальным. Телеметрия запросов, инициированная этим вызовом зависимостей, использует `id` в качестве `operation_parentId`.

Вы можете создать представление распределенной логической операции, используя `operation_Id`, `operation_parentId` и `request.id` с `dependency.id`. Эти поля также определяют причинно-следственную связь вызовов телеметрии.

В среде микрослужб трассировки компонентов могут отправляться в разные хранилища. Каждый компонент может иметь собственный ключ инструментирования в Application Insights. Чтобы получить данные телеметрии для логической операции, необходимо запросить данные из каждого хранилища. Если количество элементов для хранения очень велико, вам потребуется подсказка о том, где продолжить поиск. Модель данных Application Insights определяет два поля (`request.source` и `dependency.target`) для решения этой проблемы. Первое поле определяет компонент, который инициировал запрос зависимости, а второе — компонент, который вернул ответ вызова зависимостей.

## <a name="example"></a>Пример

Давайте рассмотрим пример приложения под названием Stock Prices, в котором показывается текущая рыночная стоимость акций. При этом используется внешний интерфейс API с именем `Stock`. В приложении Stock Prices есть страница c именем `Stock page`, которая открывается в браузере клиента с помощью `GET /Home/Stock`. Приложение запрашивает API `Stock` с помощью HTTP-вызова `GET /api/stock/value`.

Вы можете проанализировать итоговые данные телеметрии, выполнив запрос:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Обратите внимание, что в результатах все элементы телеметрии используют корневой `operation_Id`. При вызове AJAX, осуществляемом со страницы, телеметрии зависимостей присваивается личный идентификатор `qJSXU`, а в качестве `operation_ParentId` используется идентификатор pageView. Запрос сервера затем использует идентификатор Ajax как `operation_ParentId`.

| itemType   | name                      | ИД           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock page                |              | STYz               | STYz         |
| dependency | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| запрос    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependency | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Когда к внешней службе осуществлен вызов `GET /api/stock/value`, необходимо узнать идентификатор этого сервера, поэтому можно соответствующим образом задать поле `dependency.target`. Если внешняя служба не поддерживает мониторинг, то в качестве имени узла службы задается `target` (например, `stock-prices-api.com`). Однако если для идентификации службы возвращается предопределенный заголовок HTTP, то `target` содержит удостоверение службы, которое позволяет Application Insights создать распределенную трассировку, запросив телеметрию из этой службы.

## <a name="correlation-headers"></a>Заголовки корреляции

Мы работаем над созданием предложения RFC для [протокола HTTP корреляции](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Это предложение определяет два заголовка:

- `Request-Id`: Содержит глобальный уникальный идентификатор вызова.
- `Correlation-Context`: Содержит коллекцию пар "имя — значение" свойств распределенной трассировки.

Стандарт также определяет две схемы создания `Request-Id` — неструктурированная и иерархическая. В неструктурированной схеме для коллекции `Correlation-Context` определяется хорошо известный ключ `Id`.

Служба Application Insights определяет [расширение](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) для протокола HTTP корреляции. Она использует пары "имя — значение" `Request-Context` для распространения коллекции свойств, используемых непосредственным вызывающим или вызываемым. Пакет SDK для Application Insights использует этот заголовок, чтобы задать значения полей `dependency.target` и `request.source`.

### <a name="w3c-distributed-tracing"></a>Распределенная трассировка W3C

Мы переходим на [формат распределенной трассировки W3C](https://w3c.github.io/trace-context/). Он определяет следующее:

- `traceparent`: Содержит глобальный уникальный идентификатор операции и уникальный идентификатор вызова.
- `tracestate`: Содержит специфический контекст трассировки системы.

#### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Включение поддержки распределенной трассировки W3C для классических приложений ASP.NET

Эта функция доступна в пакетах `Microsoft.ApplicationInsights.Web` и `Microsoft.ApplicationInsights.DependencyCollector`, начиная с версии 2.8.0-beta1.
Она отключена по умолчанию. Чтобы ее включить, измените `ApplicationInsights.config`:

- В разделе `RequestTrackingTelemetryModule` добавьте элемент `EnableW3CHeadersExtraction` со значением `true`.
- В разделе `DependencyTrackingTelemetryModule` добавьте элемент `EnableW3CHeadersInjection` со значением `true`.

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Включение поддержки распределенной трассировки W3C для приложений ASP.NET Core

Эта функция доступна в `Microsoft.ApplicationInsights.AspNetCore` версии 2.5.0-beta1 и в `Microsoft.ApplicationInsights.DependencyCollector` версии 2.8.0-beta1.
Она отключена по умолчанию. Чтобы ее включить, задайте для `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` значение `true`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

#### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Включение поддержки распределенной трассировки консорциума W3C для приложений Java

- **Входящая конфигурация**

  - Для приложений Java EE добавьте следующее к тегу `<TelemetryModules>` в ApplicationInsights.xml:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
  - Для приложения Spring Boot добавьте следующие свойства:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Исходящая конфигурация**

  Добавьте к файлу AI-Agent.xml следующее:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > По умолчанию включен режим обратной совместимости, и параметр `enableW3CBackCompat` является необязательным. Используйте его, только если вы хотите отключить режим обратной совместимости.
  >
  > В идеале его можно отключить, когда все ваши службы обновлены до новой версии пакетов SDK, поддерживающих протокол консорциума W3C. Мы настоятельно рекомендуем перейти к этим новым пакетам SDK как можно скорее.

> [!IMPORTANT]
> Убедитесь, что входящая и исходящая конфигурации точно совпадают.

## <a name="opentracing-and-application-insights"></a>OpenTracing и Application Insights

[Спецификации модели данных OpenTracing](https://opentracing.io/) и модели данных Application Insights сопоставлены следующим образом:

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` с `span.kind = server`                  |
| `Dependency`                          | `Span` с `span.kind = client`                  |
| `Id` из `Request` и `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` типа `ChildOf` (родительский диапазон)   |

Дополнительные сведения см. в статье [Модель данных телеметрии Application Insights](../../azure-monitor/app/data-model.md). 

Определения основных понятий OpenTracing см. в [спецификации](https://github.com/opentracing/specification/blob/master/specification.md) и [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-net"></a>Корреляция данных телеметрии в .NET

Со временем в .NET было определено несколько способов корреляции данных телеметрии и журналов диагностики:

- `System.Diagnostics.CorrelationManager` позволяет отслеживать [LogicalOperationStack и ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). 
- `System.Diagnostics.Tracing.EventSource` и трассировка событий Windows(ETW) определяет метод [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx).
- `ILogger` использует [области журналов](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). 
- Windows Communication Foundation (WCF) и HTTP подключают распространение "текущего" контекста.

Однако эти методы не обеспечивали поддержку автоматической распределенной трассировки. `DiagnosticSource` — это способ обеспечить поддержку автоматической корреляции между компьютерами. Библиотеки .NET поддерживают DiagnosticsSource и разрешают автоматическое распространение контекста корреляции между компьютерами, используя транспортный протокол, например HTTP.

В [руководстве пользователя по классу Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) в `DiagnosticSource` представлены общие сведения об отслеживании действий.

ASP.NET Core 2.0 поддерживает извлечение заголовков HTTP и запуск нового действия.

`System.Net.HttpClient`, начиная с версии 4.1.0, поддерживает автоматическое внедрение заголовков HTTP корреляции и отслеживание вызова HTTP как действия.

Для классического ASP.NET доступен новый HTTP-модуль [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/). Этот модуль реализует корреляцию данных телеметрии с помощью `DiagnosticSource`. Он запускает действие, исходя из заголовков входящего запроса. Он также сопоставляет данные телеметрии с разных этапов обработки запросов, даже в тех случаях, когда каждый этап обработки Internet Information Services (IIS) работает на другом управляемом потоке.

Пакет SDK для Application Insights, начиная с версии 2.4.0-beta1, использует `DiagnosticSource` и `Activity` для сбора данных телеметрии и их связывания с текущим действием.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Корреляция данных телеметрии в пакете SDK для Java

[Пакет SDK для Java Application Insights](../../azure-monitor/app/java-get-started.md) поддерживает автоматическую корреляцию данных телеметрии, начиная с версии 2.0.0. Он автоматически заполняет `operation_id` для всех данных телеметрии (таких как трассировки, исключения, пользовательские события), генерируемых в рамках запроса. Он также распространяет заголовки корреляции (описаны выше) для вызовов между службами по протоколу HTTP при наличии настроенного [агента Java SDK](../../azure-monitor/app/java-agent.md).

> [!NOTE]
> Функция корреляции поддерживает только вызовы, выполненные через HTTP-клиент Apache. Если применяется шаблон Spring Rest или Feign, они оба могут использоваться с HTTP-клиентом Apache в неявном виде.

В настоящее время автоматическое распространение контекстной информации не поддерживается технологиями обмена сообщениями (например, Kafka, RabbitMQ, Служебная шина Azure). Тем не менее можно запрограммировать такие сценарии вручную с помощью API `trackDependency` и `trackRequest`. При этом телеметрия зависимостей представляет сообщение, поставленное в очередь поставщиком, а запрос представляет сообщение, обрабатываемое объектом-получателем. В этом случае и `operation_id`, и `operation_parentId` должны быть указаны в свойствах сообщений.

<a name="java-role-name"></a>
## <a name="role-name"></a>Имя роли

В некоторых случаях может потребоваться настроить способ отображения названий компонентов в [схеме приложений](../../azure-monitor/app/app-map.md). Чтобы сделать это, можно вручную задать `cloud_RoleName` одним из следующих способов.

- Если вы используете Spring Boot с начальным набором Application Insights Spring Boot, все, что необходимо изменить, — задать пользовательское имя приложения в файле application.properties.

  `spring.application.name=<name-of-app>`

  Начальное приложение Spring Boot автоматически назначает для `cloudRoleName` значение, указанное для свойства `spring.application.name`.

- Если вы используете `WebRequestTrackingFilter`, `WebAppNameContextInitializer` автоматически задаст имя приложения. Добавьте следующее в файл конфигурации (ApplicationInsights.xml):

  ```XML
  <ContextInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
  </ContextInitializers>
  ```

- Если вы используете класс контекста облака:

  ```Java
  telemetryClient.getContext().getCloud().setRole("My Component Name");
  ```

## <a name="next-steps"></a>Дальнейшие действия

- Напишите [пользовательскую телеметрию](../../azure-monitor/app/api-custom-events-metrics.md).
- Узнайте об [установке свойства cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud_rolename) для других пакетов SDK.
- Подключите все компоненты своей микрослужбы с помощью Application Insights. Ознакомьтесь со сведениями о [поддерживаемых платформах](../../azure-monitor/app/platforms.md).
- В [этой статье](../../azure-monitor/app/data-model.md) представлена модель данных для Application Insights.
- Вы можете узнать, как [расширять и фильтровать данные телеметрии](../../azure-monitor/app/api-filtering-sampling.md).
- Просмотрите [справочник по конфигурации в Application Insights](configuration-with-applicationinsights-config.md).
