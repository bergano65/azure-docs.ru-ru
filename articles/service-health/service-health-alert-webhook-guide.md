---
title: Отправка уведомлений о здоровье службы Azure через веб-крючки
description: Отправка персонализированных уведомлений о событиях работоспособности службы в существующую систему управления проблемами.
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: 2609a267bd151354f83482ab16c4b9345aa88cc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062856"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>Используйте веб-крюк для настройки уведомлений о работоспособности для систем управления проблемами

В этой статье показано, как настроить оповещения о здоровье службы Azure для отправки данных через веб-крючки в существующую систему уведомлений.

Вы можете настроить оповещения службы работы, чтобы уведомить вас по текстовому сообщению или электронной почте, когда инцидент службы Azure влияет на вас.

Но, возможно, уже есть существующая внешняя система уведомлений, которую вы предпочитаете использовать. В этой статье определены наиболее важные части полезной нагрузки webhook. В нем описывается, как создавать пользовательские оповещения, чтобы уведомить вас о возникновении соответствующих проблем со службой.

Если вы хотите использовать преднакнастроенную интеграцию, см.:
* [Настройка оповещений с помощью ServiceNow](service-health-alert-webhook-servicenow.md).
* [Настройка оповещений с помощью PagerDuty](service-health-alert-webhook-pagerduty.md).
* [Настройка оповещений с помощью OpsGenie](service-health-alert-webhook-opsgenie.md).

**Смотреть вступительное видео:**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>Настройка пользовательского уведомления с помощью полезной нагрузки Webhook Service Health
Чтобы настроить собственную интеграцию веб-крючка, необходимо разобрать полезную нагрузку JSON, отправленную через уведомление Service Health.

Смотрите [пример](../azure-monitor/platform/activity-log-alerts-webhook.md) `ServiceHealth` полезной нагрузки webhook.

Вы можете подтвердить, что это предупреждение о `context.eventSource == "ServiceHealth"`работоспособности службы, посмотрев на. Наиболее актуальными являются следующие свойства:
- **data.context.activityLog.status**
- **data.context.activityLog.level**
- **data.context.activityLog.subscriptionId**
- **data.context.activityLog.properties.title**
- **data.context.activityLog.properties.impactStartTime**
- **data.context.activityLog.properties.communication**
- **data.context.activityLog.properties.impactedServices**
- **data.context.activityLog.properties.trackingId**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>Создание ссылки на панель мониторинга работоспособности службы для инцидента
Вы можете создать прямую ссылку на панель мониторинга работы службы на рабочем столе или мобильном устройстве, создав специализированный URL. Используйте *trackingId* и первые три и последние три цифры вашей *подпискиВ* этом формате:

https<i></i>://app.azure.com/h/*&lt;trackingId&gt;*/*&lt;первые три и последние три цифры&gt; subscriptionId*

Например, если ваша *подпискаid* bba14129-e895-429b-8809-278e836ecdb3 и ваш *trackingId* 0DET-URB, ваш URL-адрес службы здравоохранения:

https://app.azure.com/h/0DET-URB/bbadb3<i></i>

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>Используйте уровень для определения серьезности проблемы
От самой низкой до самой высокой степени тяжести, **свойство уровня** в полезной нагрузке может быть *информационным,* *Предупреждение,* *Ошибка*, или *критический*.

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>Разобрать затронутые службы для определения сферы инцидента
Оповещения службы здоровья могут информировать вас о проблемах в нескольких регионах и службах. Чтобы получить полную информацию, необходимо разобрать `impactedServices`значение.

Содержимое, находяцкоторого внутри, представляет собой сбежавшую строку [JSON,](https://json.org/) которая, когда она не сбежит, содержит другой объект JSON, который можно регулярно разбирать. Пример:

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

Становится:

```json
[
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia East"
         },
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"Alerts & Metrics"
   },
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"App Service"
   }
]
```

Этот пример показывает проблемы для:
- "Оповещения & метрики" в Австралии Восточной и Австралии юго-востоке.
- "App Service" в Австралии на юго-востоке.

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>Проверьте интеграцию веб-крючка через запрос HTTP POST

Выполните следующие действия.

1. Создайте полезную нагрузку работоспособности службы, которую вы хотите отправить. Например, пример полезной нагрузки [Webhooks для оповещения о деятельности Azure](../azure-monitor/platform/activity-log-alerts-webhook.md)для webhook s-службы приведен ы например, приведенной службой webhook.

1. Создайте запрос HTTP POST следующим образом:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   Вы должны получить ответ "2XX - Успешный".

1. Откройте [PagerDuty](https://www.pagerduty.com/) и убедитесь, что интеграция настроена успешно.

## <a name="next-steps"></a>Дальнейшие действия
- Просмотрите [схему оповещения о журнале активности webhook.](../azure-monitor/platform/activity-log-alerts-webhook.md) 
- Узнайте об [уведомлениях службы о работоспособности](../azure-monitor/platform/service-notifications.md).
- Подробнее о [группах действий.](../azure-monitor/platform/action-groups.md)