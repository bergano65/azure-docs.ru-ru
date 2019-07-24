---
title: Настройка уведомлений о работоспособности служб Azure для существующих систем управления проблемами с помощью веб-перехватчика
description: Отправка персонализированных уведомлений о событиях работоспособности служб в существующую систему управления проблемами.
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.workload: Supportability
ms.date: 3/27/2018
ms.openlocfilehash: 8f84b43519c197797b39397cfd15c4f90444177c
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854384"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>Использование веб-перехватчика для настройки уведомлений о работоспособности для систем управления проблемами

В этой статье показано, как настроить оповещения о работоспособности служб Azure для отправки данных через веб-перехватчики в существующую систему уведомлений.

Вы можете настроить оповещения о работоспособности службы, чтобы уведомить вас по текстовому сообщению или электронной почте, когда на вас влияет обращение к службе Azure.

Но у вас уже может быть существующая внешняя система уведомлений, которую вы предпочитаете использовать. В этой статье указаны наиболее важные части полезных данных веб-перехватчика. В нем описывается создание настраиваемых оповещений для уведомления при возникновении соответствующих проблем службы.

Если вы хотите использовать предварительно настроенную интеграцию, см.:
* [Настройка оповещений с помощью ServiceNow](service-health-alert-webhook-servicenow.md).
* [Настройка оповещений с помощью PagerDuty](service-health-alert-webhook-pagerduty.md).
* [Настройка оповещений с помощью OpsGenie](service-health-alert-webhook-opsgenie.md).

**Просмотрите вступительное видео:**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>Настройка пользовательского уведомления с помощью полезных данных веб-перехватчика службы работоспособности служб
Чтобы настроить собственную интеграцию с веб-перехватчиком, необходимо проанализировать полезные данные JSON, которые отправляются через уведомление о работоспособности службы.

См. [Пример](../azure-monitor/platform/activity-log-alerts-webhook.md) `ServiceHealth` полезных данных веб-перехватчика.

Чтобы убедиться, что это предупреждение о работоспособности службы, просмотрите `context.eventSource == "ServiceHealth"`. Наиболее актуальны следующие свойства:
- **Data. Context. activityLog. status**
- **Data. Context. activityLog. Level**
- **Data. Context. activityLog. subscriptionId**
- **Data. Context. activityLog. Properties. Title**
- **Data. Context. activityLog. Properties. Импактстарттиме**
- **Data. Context. activityLog. Properties. Обмен данными**
- **Data. Context. activityLog. Properties. Импактедсервицес**
- **Data. Context. activityLog. Properties. trackingId**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>Создание ссылки на панель мониторинга работоспособности службы для инцидента
Вы можете создать прямую ссылку на панель мониторинга работоспособности службы на настольном или мобильном устройстве, создав специальный URL-адрес. Используйте *trackingId* и первые три и последние три цифры *SubscriptionId* в следующем формате:

HTTPS<i></i>://App.Azure.com/h/ *&lt;trackingId&gt;* /*первые три и последние три цифры SubscriptionId&gt; &lt;*

Например, если значение *SubscriptionId* — bba14129-e895-429b-8809-278e836ecdb3, а *trackingId* — 0DET-УРБ, URL-адрес работоспособности службы будет следующим:

HTTPS<i></i>://App.Azure.com/h/0DET-URB/bbadb3

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>Используйте уровень, чтобы определить серьезность проблемы.
Свойство **Level** в полезных данных от низкого до высокого уровня может быть информационным, предупреждением , *ошибкой*или критическим .

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>Анализ затронутых служб для определения области инцидента
Оповещения о работоспособности служб могут сообщать о проблемах в нескольких регионах и службах. Чтобы получить полные сведения, необходимо выполнить синтаксический анализ значения `impactedServices`.

Содержимое, которое находится внутри, представляет собой экранированную строку [JSON](https://json.org/) , которая, в случае неэкранирования, содержит другой объект JSON, который можно регулярно анализировать. Пример:

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

обретает

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

В этом примере показаны проблемы для:
- "Оповещения & метрики" в Восточная Австралия и Юго-Восточной Австралии.
- "Служба приложений" в Юго-Восточной Австралии.

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>Тестирование интеграции веб-перехватчика с помощью запроса HTTP POST

Выполните следующие действия.

1. Создайте полезные данные работоспособности службы, которые необходимо отправить. См. пример полезных данных веб-перехватчика работоспособности службы на [веб-перехватчиках оповещений журнала действий Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Создайте запрос HTTP POST следующим образом:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   Вы должны получить ответ "2XX-успех".

1. Откройте [PagerDuty](https://www.pagerduty.com/) и убедитесь, что интеграция настроена успешно.

## <a name="next-steps"></a>Следующие шаги
- Просмотрите схему веб-перехватчика оповещений журнала действий в статье [Объекты webhook для оповещений журнала действий Azure](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Дополнительные сведения об уведомлениях о работоспособности службы см. в [этой статье](../azure-monitor/platform/service-notifications.md).
- Дополнительные сведения о группах действий см. в статье [Создание групп действий и управление ими на портале Azure](../azure-monitor/platform/action-groups.md).