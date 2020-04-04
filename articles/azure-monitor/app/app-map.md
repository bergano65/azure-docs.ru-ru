---
title: Схема сопоставления приложений в Azure Application Insights | Документация Майкрософт
description: Мониторинг топологий сложных приложений с помощью схемы приложения
ms.topic: conceptual
ms.date: 03/15/2019
ms.reviewer: sdash
ms.openlocfilehash: 0823dd5d880c778f9b7a231ac14f1cbba1940927
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657393"
---
# <a name="application-map-triage-distributed-applications"></a>Схема приложений: рассмотрение распределенных приложений

Схема приложений позволяет выявлять сбои и проблемы с производительностью во всех компонентах распределенного приложения. Каждый узел на схеме представляет компонент приложения или его зависимости с такими данными, отображая КПЭ работоспособности и состояние оповещений. Вы можете щелкнуть любой компонент, чтобы получить более подробные диагностические данные (например, события Application Insights). Если приложение использует службы Azure, то можно также выбрать данные системы диагностики Azure, например рекомендации Помощника по базам данных SQL.

## <a name="what-is-a-component"></a>Что такое компонент?

Компоненты — это независимо развертываемые части распределенного приложения или приложения для микрослужб. Разработчики и операционные команды могут просматривать данные телеметрии, созданные компонентами приложения, на уровне кода. 

* Компоненты отличаются от наблюдаемых внешних зависимостей, таких как база данных SQL или концентратор событий, к которым команды или организации могут не иметь доступа (код или данные телеметрии).
* Компоненты выполняются на любом количестве экземпляров серверов, ролей или контейнеров.
* К компонентам могут относиться отдельные ключи инструментирования Application Insights (даже из разных подписок) или разные роли, относящиеся к одному такому ключу. В предварительной версии схемы компоненты отображаются, независимо от того, как они настроены.

## <a name="composite-application-map"></a>Схема сопоставления составных приложений

Вы увидите все данные топологии на нескольких уровнях компонентов, связанных с приложением. Компонентами могут быть различные ресурсы Application Insights или различные роли в одном ресурсе. Схема приложений находит компоненты, отслеживая HTTP-вызовы зависимостей, выполняемые между серверами, на которых установлен пакет SDK для Application Insights. 

В этом интерфейсе сначала осуществляется последовательное обнаружение компонентов. При первой загрузке карты приложения срабатывает набор запросов для обнаружения компонентов, связанных с этим компонентом. Возле кнопки в верхнем левом углу будет обновляться число компонентов приложения по мере их обнаружения. 

Если вы нажмете кнопку "Обновить компоненты сопоставления", схема обновится со всеми компонентами, обнаруженными до этого момента. В зависимости от сложности приложения загрузка может занять минуту.

Если все компоненты представляют собой роли в одном ресурсе Application Insights, этот шаг обнаружения не требуется. При начальной загрузке такого приложения загрузятся и все его компоненты.

![Снимок экрана со схемой приложения](media/app-map/app-map-001.png)

Одной из ключевых задач этого интерфейса является возможность визуализировать сложные топологии с сотнями компонентов.

Щелкните любой компонент, чтобы просмотреть соответствующие данные аналитики и перейти к интерфейсу для рассмотрения производительности и сбоев этого компонента.

![Всплывающий элемент](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Анализ сбоев

Нажмите кнопку **Анализ сбоев**, чтобы открыть панель сбоев.

![Снимок экрана с кнопкой "Анализ сбоев"](media/app-map/investigate-failures.png)

![Снимок экрана с интерфейсом анализа сбоев](media/app-map/failures.png)

### <a name="investigate-performance"></a>Исследование производительности

Чтобы устранить неполадки в производительности, выберите **исследовать производительность.**

![Снимок экрана с кнопкой "Анализ работы"](media/app-map/investigate-performance.png)

![Снимок экрана с интерфейсом анализа производительности](media/app-map/performance.png)

### <a name="go-to-details"></a>Переход к сведениям

Выберите **подробную информацию,** чтобы изучить сквозной опыт транзакций, который может предложить представления вплоть до уровня стека вызовов.

![Снимок экрана с кнопкой "Перейти к сведениям"](media/app-map/go-to-details.png)

![Снимок экрана с окном сведений о сквозных транзакциях](media/app-map/end-to-end-transaction.png)

### <a name="view-logs-analytics"></a>Просмотр журналов (аналитика)

Чтобы заставить заранее и исследовать данные приложений дальше, нажмите **на просмотр в журналах (Аналитика)**.

![Снимок экрана с кнопкой "Просмотр в службе Analytics"](media/app-map/view-logs.png)

![Скриншот опыта аналитики. Линейный график, обобщающий среднюю продолжительность ответа запроса за последние 12 часов.](media/app-map/log-analytics.png)

### <a name="alerts"></a>видны узлы

Чтобы просмотреть активные оповещения и основные правила, которые их активируют, щелкните **Оповещения**.

![Снимок экрана с кнопкой "Оповещения"](media/app-map/alerts.png)

![Снимок экрана с интерфейсом службы Analytics](media/app-map/alerts-view.png)

## <a name="set-cloud-role-name"></a>Установка имени роли облака

Карта приложений использует свойство **имени роли облака** для идентификации компонентов на карте. SDK Application Insights автоматически добавляет свойство имени роли облака в телеметрию, излучаемую компонентами. Например, SDK добавит имя веб-сайта или имя роли службы в свойство имени роли облака. Но бывают случаи, когда необходимо переопределить значение по умолчанию. Чтобы переопределить имя роли облака и изменить то, что отображается на карте приложений:

# <a name="netnetcore"></a>[.NET/.NetCore](#tab/net)

**Напишите пользовательские Телеметрия Initializer как унаситритель, как ниже.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

**ASP.NET приложения: Загрузите инициализатор на активную телеметрическуюконфигурацию**

В ApplicationInsights.config :

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

Альтернативный метод для ASP.NET веб-приложений заключается в мгновенном воспроизведении инициализатора в коде, например, в Global.aspx.cs:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

> [!NOTE]
> Добавление инициализатора с использованием `ApplicationInsights.config` или использованием `TelemetryConfiguration.Active` не является действительным для ASP.NET основных приложений. 

**ASP.NET основные приложения: Загрузить инициализатор на Телеметрическую Конфигурацию**

Для [ASP.NET основных](asp-net-core.md#adding-telemetryinitializers) приложений добавление нового `TelemetryInitializer` выполняется путем добавления его в контейнер для инъекций зависимости, как показано ниже. Это делается `ConfigureServices` методом `Startup.cs` вашего класса.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

# <a name="java"></a>[Java](#tab/java)

Начиная с Application Insights Java SDK 2.5.0, вы `<RoleName>` можете `ApplicationInsights.xml` указать имя роли облака, добавив в файл, например.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
   <RoleName>** Your role name **</RoleName>
   ...
</ApplicationInsights>
```

Если вы используете Spring Boot с начальным набором Application Insights Spring Boot, все, что необходимо изменить, — задать пользовательское имя приложения в файле application.properties.

`spring.application.name=<name-of-app>`

Стартер Spring Boot автоматически присвоит имя роли облака значению, которое вы вводите для spring.application.name свойства.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Альтернативный метод для Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```
---

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Понимание имени роли облака в контексте Карты Приложений

Что касается того, как думать об **названии роли облака,** может быть полезно посмотреть на карту приложений, которая имеет несколько имен ролей облака настоящее время:

![Снимок экрана со схемой приложения](media/app-map/cloud-rolename.png)

На карте приложений выше каждое из имен в зеленых коробках являются значениями названий роли облака для различных аспектов данного распределенного приложения. Так что для этого приложения `Authentication` `acmefrontend`его `Inventory Management`роли `Payment Processing Worker Role`состоят из: , , , a . 

В случае этого приложения каждое из этих названий роли облака также представляет собой другой уникальный ресурс Application Insights со своими собственными ключами приборов. Поскольку владелец этого приложения имеет доступ к каждому из этих четырех разрозненных ресурсов Application Insights, Application Map может сшить карту базовых отношений.

Для [официальных определений:](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93)

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Кроме того, **экземпляр роли облака** может быть полезен для сценариев, где название роли **облака** говорит вам, что проблема находится где-то в вашем веб-фронт-энде, но вы можете работать с веб-фронт-эндом через несколько сбалансированных с нагрузкой серверов, чтобы иметь возможность просверлить в слое глубже через запросы Kusto и зная, если проблема влияет на все веб-серверы/экземпляры или только один может быть чрезвычайно важным.

Сценарий, в котором может потребоваться переопределить значение для экземпляра роли облака, может быть, если ваше приложение работает в контейнерной среде, где только знание отдельного сервера может быть недостаточно информации, чтобы найти заданную проблему.

Для получения дополнительной информации о том, как переопределить свойство названия роли облака с помощью инициализаторов телеметрии, [см.](api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)

## <a name="troubleshooting"></a>Устранение неполадок

Если схема приложений не работает должным образом, попробуйте сделать следующее:

### <a name="general"></a>Общее

1. Убедитесь, что вы используете официально поддерживаемый пакет SDK. Неподдерживаемые пакеты SDK и пакеты SDK сообщества могут не поддерживать корреляцию.

    Список поддерживаемых пакетов SDK см. в этой [статье](https://docs.microsoft.com/azure/application-insights/app-insights-platforms).

2. Обновите все компоненты до последней версии пакета SDK.

3. При использовании Функций Azure с C# выполните обновление до [Функций версии 2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Подтверждение [имени роли облака](#set-cloud-role-name) правильно настроено.

5. Если вы пропустили зависимость, убедитесь, что она находится в списке [автоматически собранных зависимостей](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Если нет, вы можете по-прежнему отследить ее вручную с помощью [вызова зависимости отслеживания](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Слишком много узлов на карте

Карта приложения конструирует узло приложения для каждого уникального имени роли облака, присутствующем узло в телеметрии запроса, и узло зависимости для каждого уникального сочетания типа, цели и имени роли облака в телеметрии зависимости. Если в телеметрии более 10 000 узлов, Application Map не сможет получить все узлы и ссылки, поэтому ваша карта будет неполной. В этом случае при просмотре карты появится предупреждающее сообщение.

Кроме того, Карта приложений поддерживает только до 1000 отдельных негруппированных узлов, отображаемых одновременно. Карта приложений снижает сложность зрения, группируя зависимости, которые имеют один и тот же тип и абонентов, но если телеметрия имеет слишком много уникальных имен ролей облака или слишком много типов зависимостей, эта группировка будет недостаточной, и карта не сможет визуализировать.

Чтобы исправить это, необходимо изменить приборы, чтобы правильно установить имя роли облака, тип зависимости и целевые поля зависимости.

* Цель зависимости должна представлять логическое название зависимости. Во многих случаях это эквивалентно названию зависимости сервера или ресурса. Например, в случае зависимостей HTTP он устанавливается на имя хоста. Он не должен содержать уникальные идоловы или параметры, которые изменяются от одного запроса к другому.

* Тип зависимости должен представлять логический тип зависимости. Например, http, S'L или Azure Blob являются типичными типами зависимостей. Он не должен содержать уникальные идоловые иудиции.

* Цель названия роли облака описана в [вышеуказанном разделе.](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name)

## <a name="portal-feedback"></a>Отзывы на портале

Чтобы оставить свой отзыв, воспользуйтесь соответствующей функцией.

![Изображение MapLink-1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать больше о том, как корреляция работает в Application Insights проконсультируйтесь с [статьей о телеметрии корреляции](correlation.md).
* Сквозной [диагностический опыт транзакций](transaction-diagnostics.md) коррелирует телеметрию сервера со всех компонентов Application Insights в единое представление.
* Для продвинутых сценариев корреляции в ASP.NET Core и ASP.NET проконсультируйтесь со статьей [трек пользовательских операций.](custom-operations-tracking.md)
