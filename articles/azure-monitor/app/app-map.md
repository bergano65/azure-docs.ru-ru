---
title: Схема сопоставления приложений в Azure Application Insights | Документация Майкрософт
description: Мониторинг топологий сложных приложений с помощью схемы приложения
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/15/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 73cf6fd1c20f2e4208d1f7c28a756f28a2fad839
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302573"
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

В этом интерфейсе сначала осуществляется последовательное обнаружение компонентов. При первой загрузке схемы приложения запускается набор запросов для обнаружения компонентов, связанных с этим компонентом. Возле кнопки в верхнем левом углу будет обновляться число компонентов приложения по мере их обнаружения. 

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

Чтобы устранить неполадки с производительностью, нажмите кнопку **Анализ работы**.

![Снимок экрана с кнопкой "Анализ работы"](media/app-map/investigate-performance.png)

![Снимок экрана с интерфейсом анализа производительности](media/app-map/performance.png)

### <a name="go-to-details"></a>Переход к сведениям

Выберите **Перейти к сведениям** , чтобы изучить сквозную транзакцию, которая может предлагать представления на уровне стека вызовов.

![Снимок экрана с кнопкой "Перейти к сведениям"](media/app-map/go-to-details.png)

![Снимок экрана с окном сведений о сквозных транзакциях](media/app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Просмотр в службе Analytics

Чтобы запросить и исследовать данные о приложении, нажмите кнопку **Просмотр в службе Analytics**.

![Снимок экрана с кнопкой "Просмотр в службе Analytics"](media/app-map/view-in-analytics.png)

![Снимок экрана с интерфейсом службы Analytics](media/app-map/analytics.png)

### <a name="alerts"></a>Предупреждения

Чтобы просмотреть активные оповещения и основные правила, которые их активируют, щелкните **Оповещения**.

![Снимок экрана с кнопкой "Оповещения"](media/app-map/alerts.png)

![Снимок экрана с интерфейсом службы Analytics](media/app-map/alerts-view.png)

## <a name="set-cloud-role-name"></a>Задание имени облачной роли

Схема приложения использует свойство **имя роли облака** для обнаружения компонентов на карте. Пакет SDK для Application Insights автоматически добавляет свойство имени роли облака в данные телеметрии, созданные компонентами. Например, пакет SDK добавит имя веб-сайта или роли службы в свойство имя роли облака. Но бывают случаи, когда необходимо переопределить значение по умолчанию. Чтобы переопределить имя роли облака и изменить то, что отображается на схеме приложения:

### <a name="netnet-core"></a>.NET и .NET Core

**Напишите пользовательский TelemetryInitializer, как показано ниже.**

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

**ASP.NET приложения: Загрузить инициализатор в активную Телеметриконфигуратион**

В файле ApplicationInsights. config:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

Альтернативным методом для веб-приложений ASP.NET является создание экземпляра инициализатора в коде, например в Global.aspx.cs:

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
> Добавление инициализатора `ApplicationInsights.config` с помощью `TelemetryConfiguration.Active` или using недопустимо для приложений ASP.NET Core. 

**ASP.NET Core приложения: Загрузить инициализатор в Телеметриконфигуратион**

Для [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) приложений Добавление нового `TelemetryInitializer` выполняется путем добавления его в контейнер внедрения зависимостей, как показано ниже. Это делается в `ConfigureServices` методе `Startup.cs` класса.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

### <a name="nodejs"></a>Node.js

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

### <a name="java"></a>Java

Если вы используете Spring Boot с начальным набором Application Insights Spring Boot, все, что необходимо изменить, — задать пользовательское имя приложения в файле application.properties.

`spring.application.name=<name-of-app>`

Начальная загрузка с пружиной автоматически присвоит имя облачной роли значению, введенному для свойства spring.application.name.

Дополнительные сведения о корреляции Java и о настройке имени облачной роли для SpringBoot приложений извлеките этот [раздел](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name) по корреляции.

### <a name="clientbrowser-side-javascript"></a>JavaScript на стороне клиента или браузера

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Основные сведения об имени облачной роли в контексте схемы приложения

Как подумать о **имени облачной роли**, полезно рассмотреть схему приложения, в которой есть несколько имен облачных ролей:

![Снимок экрана со схемой приложения](media/app-map/cloud-rolename.png)

В схеме приложения выше каждое из имен в зеленом окне является значениями имени облачной роли для различных аспектов этого конкретного распределенного приложения. Итак, для этого приложения его роли состоят из `Authentication`: `acmefrontend`, `Inventory Management`,, `Payment Processing Worker Role`а. 

В случае этого приложения каждое из этих имен облачных ролей также представляет отдельный уникальный Application Insights ресурс с собственными ключами инструментирования. Поскольку владелец этого приложения имеет доступ к каждому из четырех разнородных Application Insightsных ресурсов, схема приложения может объединить карту базовых связей.

Для [официальных определений](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Кроме того, **экземпляр облачной роли** может быть полезен в сценариях, где **имя роли облака** сообщает о проблеме в клиентском веб-интерфейсе, но может быть запущен внешний веб-интерфейс на нескольких серверах с балансировкой нагрузки, чтобы иметь возможность подробное изучение слоя с помощью запросов Kusto и знание того, влияет ли эта ошибка на все серверы и экземпляры клиентского веб-интерфейса или только на один из них, может быть чрезвычайно важен.

Сценарий, в котором может потребоваться переопределить значение для экземпляра облачной роли, может быть в том случае, если приложение выполняется в контейнерной среде, в котором только знание отдельного сервера может оказаться недостаточно для определения конкретной проблемы.

Дополнительные сведения о том, как переопределить свойство имени роли облака с помощью инициализаторов телеметрии, см [. в разделе Добавление свойств: разделе](api-filtering-sampling.md#add-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Устранение неполадок

Если схема приложений не работает должным образом, попробуйте сделать следующее:

### <a name="general"></a>Общие сведения

1. Убедитесь, что вы используете официально поддерживаемый пакет SDK. Неподдерживаемые пакеты SDK и пакеты SDK сообщества могут не поддерживать корреляцию.

    Список поддерживаемых пакетов SDK см. в этой [статье](https://docs.microsoft.com/azure/application-insights/app-insights-platforms).

2. Обновите все компоненты до последней версии пакета SDK.

3. При использовании Функций Azure с C# выполните обновление до [Функций версии 2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Подтвердите правильность настройки [имени облачной роли](#set-cloud-role-name) .

5. Если вы пропустили зависимость, убедитесь, что она находится в списке [автоматически собранных зависимостей](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Если нет, вы можете по-прежнему отследить ее вручную с помощью [вызова зависимости отслеживания](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Слишком много узлов на карте

Схема приложений конструирует узел приложения для каждого уникального имени облачной роли, присутствующей в телеметрии запросов, и узел зависимости для каждой уникальной комбинации типа, целевого объекта и имени облачной роли в телеметрии зависимостей. Если в телеметрии более 10 000 узлов, то схема приложений не сможет получить все узлы и ссылки, поэтому ваша схема будет неполной. В этом случае при просмотре схемы появится предупреждающее сообщение.

Кроме того, схема приложений поддерживает до 1000 отдельных несгруппированных узлов, отображаемых одновременно. Схема приложений сокращает сложность визуального элемента за счет группирования зависимостей вместе с одинаковым типом и вызывающими объектами, но если в телеметрии слишком много уникальных имен облачных ролей или слишком много типов зависимостей, это группирование будет недостаточным, и Map не сможет обрабатывает.

Чтобы устранить эту проблему, необходимо изменить инструментирование, чтобы правильно задать имя роли облака, тип зависимости и целевые поля зависимости.

* Целевой объект зависимости должен представлять логическое имя зависимости. Во многих случаях это эквивалентно имени сервера или ресурса зависимости. Например, в случае с зависимостями HTTP для него задается имя узла. Он не должен содержать уникальные идентификаторы или параметры, которые изменяются от одного запроса к другому.

* Тип зависимости должен представлять логический тип зависимости. Например, HTTP, SQL или BLOB-объект Azure являются типичными типами зависимостей. Он не должен содержать уникальные идентификаторы.

* Назначение имени облачной роли описано в [приведенном выше разделе](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name).

## <a name="portal-feedback"></a>Отзывы на портале

Чтобы оставить свой отзыв, воспользуйтесь соответствующей функцией.

![Изображение MapLink-1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Следующие шаги

* [Корреляция данных телеметрии в Application Insights](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)
