---
title: Схемы для предупреждений Центра безопасности Azure
description: В этой статье описаны различные схемы, используемые Центром безопасности Azure для оповещения о безопасности.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2020
ms.author: memildin
ms.openlocfilehash: 19ca17f66f6818ed4c3ef532e2030cc03f0e73ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062952"
---
# <a name="security-alerts-schemas"></a>Схемы предупреждений безопасности

Пользователи стандартного уровня Центра безопасности Azure получают оповещения о безопасности, когда Центр безопасности обнаруживает угрозы для своих ресурсов.

Эти оповещения безопасности можно просматривать на страницах Azure Security Center **по защите угроз** или через внешние инструменты, такие как:

- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) - облачное SIEM от Microsoft. Разъем Sentinel получает оповещения из Центра безопасности Azure и отправляет их в [рабочее пространство для анализа журналов](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) для Azure Sentinel.
- Сторонние SIEMs - Используйте [инструменты непрерывного экспорта](continuous-export.md) Центра безопасности для [отправки данных в центры событий Azure.](https://docs.microsoft.com/azure/event-hubs/) Затем интегрируйте данные Event Hub с сторонним SIEM.
- [REST API](https://docs.microsoft.com/rest/api/securitycenter/) - Если вы используете REST API для [online Alerts API documentation](https://docs.microsoft.com/rest/api/securitycenter/alerts)доступа к оповещениям, см.

Если вы используете какие-либо программные методы для использования оповещений, вам понадобится правильная схема, чтобы найти поля, которые имеют отношение к вам. Кроме того, если вы экспортируете в концентратор событий или пытаетесь запустить автоматизацию рабочего процесса с помощью универсальных разъемов HTTP, используйте схемы для правильного разбора объектов JSON.

>[!IMPORTANT]
> Схема немного отличается для каждого из этих сценариев, поэтому убедитесь, что вы выберите соответствующую вкладку ниже.


## <a name="the-schemas"></a>Схемы 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[Автоматизация рабочего процесса и непрерывный экспорт в центр событий](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>Пример JSON для предупреждений, отправленных в Logic Apps, Event Hub и сторонние SIEM

Ниже вы найдете схему событий оповещения, передаваемых:

- Экземпляры Azure Logic App, настроенные в автоматизации рабочего процесса Центра безопасности
- Концентратор событий Azure с использованием функции непрерывного экспорта Центра безопасности

Для получения дополнительной информации о [Automate responses to alerts and recommendations](workflow-automation.md)функции автоматизации рабочего процесса см.
Для получения дополнительной информации о непрерывном экспорте, см [Экспорт оповещения и рекомендации](continuous-export.md).

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Рабочие пространства Azure Sentinel и Log Analytics](#tab/schema-sentinel)

Разъем Sentinel получает оповещения из Центра безопасности Azure и отправляет их в рабочее пространство для аналитики журналов для Azure Sentinel. 

Для создания случая Sentinel или инцидента с помощью предупреждений Центра безопасности вам понадобится схема для тех предупреждений, которые показаны ниже. 

Для получения дополнительной [информации](https://docs.microsoft.com/azure/sentinel/)о Azure Sentinel см.

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Журнал действий Azure](#tab/schema-activitylog)

Аудит Центра безопасности Azure генерирует оповещения безопасности как события в журнале деятельности Azure.

Вы можете просматривать события оповещения безопасности в журнале активности, ища событие активации оповещения в показано:

[![Поиск журнала активности для события активации оповещения](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>Пример JSON для предупреждений, отправленных в журнал активности Azure

```json
{
    "channels": "Operation",
    "correlationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "description": "PREVIEW - Role binding to the cluster-admin role detected. Kubernetes audit log analysis detected a new binding to the cluster-admin role which gives administrator privileges.\r\nUnnecessary administrator privileges might cause privilege escalation in the cluster.",
    "eventDataId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "eventName": {
        "value": "PREVIEW - Role binding to the cluster-admin role detected",
        "localizedValue": "PREVIEW - Role binding to the cluster-admin role detected"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2019-12-25T18:52:36.801035Z",
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/events/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/ticks/637128967568010350",
    "level": "Informational",
    "operationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Activate Alert"
    },
    "resourceGroupName": "RESOURCE_GROUP_NAME",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-12-25T19:14:03.5507487Z",
    "subscriptionId": "SUBSCRIPTION_ID",
    "properties": {
        "clusterRoleBindingName": "cluster-admin-binding",
        "subjectName": "for-binding-test",
        "subjectKind": "ServiceAccount",
        "username": "masterclient",
        "actionTaken": "Detected",
        "resourceType": "Kubernetes Service",
        "severity": "Low",
        "intent": "[\"Persistence\"]",
        "compromisedEntity": "ASC-IGNITE-DEMO",
        "remediationSteps": "[\"Review the user in the alert details. If cluster-admin is unnecessary for this user, consider granting lower privileges to the user.\"]",
        "attackedResourceType": "Kubernetes Service"
    },
    "relatedEvents": []
}
```

### <a name="the-data-model-of-the-schema"></a>Модель данных схемы

|Поле|Описание|
|----|----|
|**Каналами**|Константа, "Операция"|
|**Correlationid**|Идентификатор оповещения Центра безопасности Azure|
|**Описание**|Описание оповещения|
|**eventDataId**|Смотрите корреляциюId|
|**eventName**|Значение и локализованные подполя Value содержат имя дисплея оповещения|
|**Категории**|Значение и локализованныеподливые подполя являются постоянными - "Безопасность"|
|**eventTimestamp**|Отметка времени UTC для того, когда было создано оповещение|
|**id**|Полностью квалифицированный идентификатор оповещения|
|**Уровень**|Постоянный, "Информационный"|
|**operationId**|Смотрите корреляциюId|
|**эксплуатацияИмя**|Поле значения является постоянным - "Microsoft.Security/locations/alerts/activate/action", а локализованное значение будет "Activate Alert" (потенциально может быть локализовано номинальной пользовательской локализации)|
|**ресурсGroupName**|Будет включать имя группы ресурсов|
|**resourceProviderName**|Значение и локализованные подполя "Стоимость" являются постоянными - "Microsoft.Security"|
|**ресурсType**|Значение и локализованные подполя "Value" являются постоянными - "Microsoft.Security/locations/alerts"|
|**resourceId**|Полностью квалифицированный идентификатор ресурсов Azure|
|**состояние**|Значение и локализованные подполя "Стоимость" являются постоянными - "Активный"|
|**subStatus**|Значение и локализованные подполя «Стоимость» пусты|
|**submissionTimestamp**|Отметка времени UTC представления событий в журнал activity Log|
|**subscriptionId**|Идентификатор подписки скомпрометированного ресурса|
|**Вариантов размещения**|Пакет JSON с дополнительными свойствами, относящимися к оповещению. Они могут меняться от одного оповещения к другому, однако следующие поля будут отображаться во всех предупреждениях:<br>- тяжесть: тяжесть нападения<br>- скомпрометированоEntity: Имя скомпрометированного ресурса<br>- RemediationSteps: Массив восстановительных мер, которые необходимо предпринять<br>- намерение: Убийство цепи намерения оповещения. Возможные намерения задокументированы в [таблице «Намерения»](alerts-reference.md#intentions)|
|**relatedEvents**|Постоянный - пустой массив|
|||





### <a name="ms-graph-api"></a>[MS Граф API](#tab/schema-graphapi)

Microsoft Graph является шлюзом для данных и разведки в Microsoft 365. Он обеспечивает единую модель программируемости, которую можно использовать для доступа к огромному объему данных в Office 365, Windows 10 и Enterprise Mobility и Security. Используйте множество данных в Microsoft Graph для создания приложений для организаций и потребителей, которые взаимодействуют с миллионами пользователей.

Схема и представление JSON для предупреждений безопасности, отправленных в MS Graph, доступны в [документации Microsoft Graph.](https://docs.microsoft.com/graph/api/resources/alert?view=graph-rest-1.0)

---


## <a name="next-steps"></a>Дальнейшие действия

В этой статье описаны схемы, которые используют инструменты защиты угроз Центра безопасности Azure при отправке информации о предупреждении безопасности.

Для получения дополнительной информации о способах доступа к предупреждениям безопасности из-за пределов Центра безопасности, смотрите следующие страницы:

- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) - облачный SIEM от Microsoft
- [Концентраторы событий Azure](https://docs.microsoft.com/azure/event-hubs/) — полностью управляемый службой приема данных в режиме реального времени
- [Функция непрерывного экспорта](continuous-export.md) Центра безопасности
- [Рабочие области журналов -](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) Azure Monitor хранит данные журнала в рабочей области Log Analytics, контейнере, который включает данные и информацию о конфигурации
