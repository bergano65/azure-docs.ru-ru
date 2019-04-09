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
ms.date: 03/14/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 11f7bb69ed408adf87d62a4af1aa4bd87e70bd6d
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009201"
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

### <a name="investigate-performance"></a>Исследование производительности

Чтобы устранить неполадки с производительностью, нажмите кнопку **Анализ работы**.

![Снимок экрана с кнопкой "Анализ работы"](media/app-map/investigate-performance.png)

![Снимок экрана с интерфейсом анализа производительности](media/app-map/performance.png)

### <a name="go-to-details"></a>Переход к сведениям

Выберите **Перейти к сведениям**, чтобы просмотреть сведения о сквозных транзакциях в интерфейсе с представлениями, которые включают данные с уровня стека вызовов.

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

## <a name="set-cloudrolename"></a>Установка свойства cloud_RoleName

В схеме приложений свойство `cloud_RoleName` используется для идентификации компонентов в схеме. Пакет SDK для Application Insights автоматически добавляет свойство `cloud_RoleName` в данные телеметрии, выдаваемые компонентами. Например, пакет SDK добавит имя веб-сайта или роли службы в свойство `cloud_RoleName`. Но бывают случаи, когда необходимо переопределить значение по умолчанию. Чтобы переопределить свойство cloud_RoleName и изменить отображение на схеме приложений:

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

Начальный набор Spring Boot автоматически назначит свойству cloudRoleName значение, введенное для свойства spring.application.name.

Дополнительные сведения о корреляции Java и настройке cloudRoleName для приложений не на основе SpringBoot см. в этом [разделе](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name) о корреляции.

### <a name="clientbrowser-side-javascript"></a>JavaScript на стороне клиента или браузера

```javascript
appInsights.queue.push(() => {
appInsights.context.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

### <a name="understanding-cloudrolename-within-the-context-of-the-application-map"></a>Основные сведения о Cloud.RoleName в контексте схему сопоставления приложений

Как далеко, как продумать Cloud.RoleName может быть полезным, рассмотрим схему сопоставления приложений с несколькими Cloud.RoleNames присутствует:

![Снимок экрана со схемой приложения](media/app-map/cloud-rolename.png)

В схеме приложения выше все имена в зеленые прямоугольники являются Cloud.RoleName/role значения для различных аспектов этого конкретного распределенного приложения. Поэтому для этого приложения его ролей состоят из: `Authentication`, `acmefrontend`, `Inventory Management`, `Payment Processing Worker Role`. 

В случае этого приложения каждый из них `Cloud.RoleNames` также представляет другой уникальный ресурс Application Insights с собственными ключами инструментирования. Так как владелец этого приложения имеет доступ к каждому из этих четырех разрозненных ресурсов Application Insights, схема сопоставления приложений — возможность Сшивать карту базовые связи.

Для [официальные определения](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Кроме того могут оказаться полезными для сценариев, где Cloud.RoleName сообщает проблема находится где-то в веб-интерфейса, но можно запустить веб-интерфейса на нескольких серверах с балансировкой нагрузки, возможность подробного в слое глубже Cloud.RoleInstance с помощью запросов Kusto и зная, если проблема влияет на все web серверов/экземпляры интерфейса, или только один может быть крайне важно.

Сценарии, где может потребоваться переопределить значение для Cloud.RoleInstance может быть, если ваше приложение работает в контейнерной среде, где необходимо знать отдельного сервера может оказаться достаточно информации, чтобы найти данной неисправности.

Дополнительные сведения о переопределении свойства cloud_RoleName с помощью инициализаторов телеметрии см. в [этом разделе](api-filtering-sampling.md#add-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Устранение неполадок

Если схема приложений не работает должным образом, попробуйте сделать следующее:

1. Убедитесь, что вы используете официально поддерживаемый пакет SDK. Неподдерживаемые пакеты SDK и пакеты SDK сообщества могут не поддерживать корреляцию.

    Список поддерживаемых пакетов SDK см. в этой [статье](https://docs.microsoft.com/azure/application-insights/app-insights-platforms).

2. Обновите все компоненты до последней версии пакета SDK.

3. При использовании Функций Azure с C# выполните обновление до [Функций версии 2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Настройте [cloud_RoleName](#set-cloud_rolename) должным образом.

5. Если вы пропустили зависимость, убедитесь, что она находится в списке [автоматически собранных зависимостей](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Если нет, вы можете по-прежнему отследить ее вручную с помощью [вызова зависимости отслеживания](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="portal-feedback"></a>Отзывы на портале

Чтобы оставить свой отзыв, воспользуйтесь соответствующей функцией.

![Изображение MapLink-1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Основные сведения о корреляции](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)