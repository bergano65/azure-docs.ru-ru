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
ms.openlocfilehash: 618453ec9857254fe14608df8091bb79bd3815b7
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65509965"
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

В этом интерфейсе сначала осуществляется последовательное обнаружение компонентов. При первой загрузке схему сопоставления приложений, активируется набор запросов, позволяет обнаружить компоненты, относящиеся к этому компоненту. Возле кнопки в верхнем левом углу будет обновляться число компонентов приложения по мере их обнаружения. 

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

### <a name="investigate-performance"></a>Анализ работы

Чтобы устранить неполадки с производительностью, нажмите кнопку **Анализ работы**.

![Снимок экрана с кнопкой "Анализ работы"](media/app-map/investigate-performance.png)

![Снимок экрана с интерфейсом анализа производительности](media/app-map/performance.png)

### <a name="go-to-details"></a>Перейти к сведениям

Выберите **перейдите сведения** исследовать возможности начала до конца транзакции, которые может предложить представления вплоть до уровня стека вызова.

![Снимок экрана с кнопкой "Перейти к сведениям"](media/app-map/go-to-details.png)

![Снимок экрана с окном сведений о сквозных транзакциях](media/app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Просмотр в службе Analytics

Чтобы запросить и исследовать данные о приложении, нажмите кнопку **Просмотр в службе Analytics**.

![Снимок экрана с кнопкой "Просмотр в службе Analytics"](media/app-map/view-in-analytics.png)

![Снимок экрана с интерфейсом службы Analytics](media/app-map/analytics.png)

### <a name="alerts"></a>Оповещения

Чтобы просмотреть активные оповещения и основные правила, которые их активируют, щелкните **Оповещения**.

![Снимок экрана с кнопкой "Оповещения"](media/app-map/alerts.png)

![Снимок экрана с интерфейсом службы Analytics](media/app-map/alerts-view.png)

## <a name="set-cloud-role-name"></a>Имя набора облачной роли

Схема сопоставления приложений использует **имени облачной роли** свойство для идентификации компонентов на карте. Пакет SDK Application Insights автоматически добавляет свойство имени роли облака данные телеметрии, генерируемые компонентами. Например пакет SDK добавит имя веб-сайта или имя службы роли к свойству имени облачной роли. Но бывают случаи, когда необходимо переопределить значение по умолчанию. Чтобы переопределить имя облачной роли и изменить то, что отображается на схеме сопоставления приложений:

### <a name="net"></a>.NET

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
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance"
            }
        }
    }
}
```

**Загрузка инициализатора**

В ApplicationInsights.config.:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

Альтернативный метод — создать экземпляр инициализатора в коде, например в Global.aspx.cs.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
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

Spring Boot starter автоматически назначить значение, введенное для свойства spring.application.name имени облачной роли.

Дополнительные сведения о Java корреляции и способы настройки специализации на облачных технологиях имен для оформления заказа без SpringBoot приложений это [разделе](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name) о корреляции.

### <a name="clientbrowser-side-javascript"></a>JavaScript на стороне клиента или браузера

```javascript
appInsights.queue.push(() => {
appInsights.context.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Основные сведения об имени облачной роли в контексте схему сопоставления приложений

Что касается как продумать **имени облачной роли**, может быть полезным, рассмотрим схему сопоставления приложений, имеющий несколько ролей имена облачных присутствует:

![Снимок экрана со схемой приложения](media/app-map/cloud-rolename.png)

В сопоставление приложения выше все имена в зеленые прямоугольники являются облачной значения имени роли для различных аспектов этого конкретного распределенного приложения. Поэтому для этого приложения его ролей состоят из: `Authentication`, `acmefrontend`, `Inventory Management`, `Payment Processing Worker Role`. 

В случае этого приложения каждый из этих имен ролей облака также представляет другой уникальный ресурс Application Insights с собственными ключами инструментирования. Так как владелец этого приложения имеет доступ к каждому из этих четырех разрозненных ресурсов Application Insights, схема сопоставления приложений — возможность Сшивать карту базовые связи.

Для [официальные определения](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Кроме того **экземпляр роли облачной** могут оказаться полезными для сценариев где **имени облачной роли** сообщает проблема находится где-то в веб-интерфейса, но можно запустить веб-интерфейса через несколько с балансировкой нагрузки серверы таким образом возможность подробного глубже с помощью запросов Kusto слоя и, зная, если проблема влияет на все web серверов/экземпляры интерфейса или только один может быть очень важна.

Сценарии, где может потребоваться переопределить значение для экземпляра роли облачных может быть, если ваше приложение работает в контейнерной среде, где необходимо знать отдельного сервера может оказаться достаточно информации, чтобы найти данной неисправности.

Дополнительные сведения о том, как переопределить свойство имени облачной роли с помощью инициализаторы телеметрии см. в разделе [Добавление свойств: разделе](api-filtering-sampling.md#add-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Устранение неполадок

Если схема приложений не работает должным образом, попробуйте сделать следующее:

### <a name="general"></a>Общие сведения

1. Убедитесь, что вы используете официально поддерживаемый пакет SDK. Неподдерживаемые пакеты SDK и пакеты SDK сообщества могут не поддерживать корреляцию.

    Список поддерживаемых пакетов SDK см. в этой [статье](https://docs.microsoft.com/azure/application-insights/app-insights-platforms).

2. Обновите все компоненты до последней версии пакета SDK.

3. При использовании Функций Azure с C# выполните обновление до [Функций версии 2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Подтвердите [имени облачной роли](#set-cloud-role-name) настроен правильно.

5. Если вы пропустили зависимость, убедитесь, что она находится в списке [автоматически собранных зависимостей](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Если нет, вы можете по-прежнему отследить ее вручную с помощью [вызова зависимости отслеживания](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Слишком много узлов на карте

Схема сопоставления приложений создает приложения для каждого имени уникальной облачной роли, присутствующих в телеметрию запросов и зависимостей узла для каждого уникального сочетания типа, целевой объект и имя облачной роли в данных телеметрии зависимостей. Если есть более чем 10 000 узлов в данных телеметрии, схема сопоставления приложений не сможете получить все узлы и связи, поэтому карты может быть неполным. В этом случае предупреждение будет отображаться при просмотре карты.

Кроме того схема сопоставления приложений поддерживает только до 1000 отдельных узлах несгруппированные, подготовке к просмотру за один раз. Схема сопоставления приложений снижает визуальной сложности путем группирования зависимости, иметь тот же тип и вызывающих объектов, но если данные телеметрии имеет слишком много имен уникальной облачной роли или слишком много типов зависимостей, этого группирования будет недостаточно, и карты будет невозможно визуализации.

Чтобы устранить эту проблему, необходимо изменить инструментирования правильно устанавливать имя облачной роли, тип зависимости и зависимости целевых полей.

* Целевой объект зависимости должен представлять логическое имя зависимости. Во многих случаях это эквивалентно сервера или имя ресурса зависимости. Например в случае зависимости HTTP ему будет присвоено имя узла. Он не должен содержать уникальные идентификаторы или параметры, которые изменяются в зависимости от одного запроса.

* Тип зависимости должен представлять логический тип зависимости. Например типичный зависимостей типам относятся HTTP, SQL или BLOB-объектов Azure. Он не должен содержать уникальные идентификаторы.

* Назначение имени облачной роли описан в [над разделом](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name).

## <a name="portal-feedback"></a>Отзывы на портале

Чтобы оставить свой отзыв, воспользуйтесь соответствующей функцией.

![Изображение MapLink-1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Корреляция данных телеметрии в Application Insights](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)
