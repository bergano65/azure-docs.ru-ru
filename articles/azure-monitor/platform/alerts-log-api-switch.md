---
title: Переключение с устаревших API оповещений Log Analytics на новые API оповещений Azure
description: Общие сведения о прекращении использования устаревших savedSearch на базе API оповещений Log Analytics и процесс переключения правила оповещений на новый интерфейс API ScheduledQueryRules с подробными сведениями об общих проблемах клиентов.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/24/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: e4e935a9c78950517623acdf8196d51793fff18a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462466"
---
# <a name="switch-api-preference-for-log-alerts"></a>Переключение предпочтений API для оповещений журнала

> [!NOTE]
> Указанное содержимое можно применять только для пользователей общедоступного облака Azure, но оно **неприменимо** для пользователей Azure GovCloud.  

До недавнего времени вы управляли правилами генерации оповещений на портале Microsoft Operations Management Suite. Новые возможности оповещений были интегрированы с различными службами в Microsoft Azure, включая Log Analytics, и мы попросили [расширить ваши правила оповещения с портала OMS в Azure](alerts-extend.md). Но чтобы свести нарушение работы клиентов к минимуму, процессу не удалось изменить программный интерфейс для его потребления в [API оповещений Log Analytics](api-alerts.md) на основе SavedSearch.

Но теперь, предупреждая пользователей, вы объявляете для Log Analytics истинную программную альтернативу Azure (см. в статье [Правила запросов по расписанию](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)), которая также отражается в разделе [Цены и выставление счетов для оповещений журнала](alerts-unified-log.md#pricing-and-billing-of-log-alerts). Дополнительные сведения о том, как управлять оповещениями журнала с помощью API, см. в разделе [Управление оповещениями журнала с помощью шаблона ресурсов Azure](alerts-log.md#managing-log-alerts-using-azure-resource-template) и [Управление оповещениями журнала с помощью PowerShell, CLI или API](alerts-log.md#managing-log-alerts-using-powershell-cli-or-api).

## <a name="benefits-of-switching-to-new-azure-api"></a>Преимущества коммутации на новый интерфейс API Azure

Существует несколько преимуществ создания и управления оповещениями с помощью [правил запросов по расписанию](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) над [устаревшими API оповещениями Log Analytics](api-alerts.md). Некоторые из них указаны ниже.

- Возможность [выполнение поиска по журналам по рабочим областях](../log-query/cross-workspace-query.md) в правилах оповещения и расширения внешних ресурсов, например рабочих областей Log Analytics или даже приложения Application Insights.
- При использовании нескольких полей для группировки в запросе с помощью [scheduleQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) пользователь может указать поле для агрегирования на портале Azure.
- Создание правила генерации оповещений как единый ресурс за один раз без необходимости создавать три уровня ресурсов, как с [устаревшими API оповещения Log Analytics](api-alerts.md).
- Единый программный интерфейс для всех вариантов оповещений журнала на основе запросов в Azure — новые [правила запросов по расписанию](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), которые можно использовать для управления правилами для Log Analytics, а также для Application Insights.
- Все новые функции оповещений журнала и будущие разработки будут доступны только через новые [правила запросов по расписанию](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Процесс коммутации с устаревших оповещений журнала API

Процесс перемещения правил генерации оповещений из [устаревших оповещений API Log Analytics](api-alerts.md) не затрагивает определение, запрос или конфигурацию оповещения каким-либо образом. Пользователи могут использовать любое [устаревшие оповещение API Log Analytics](api-alerts.md) или новое [правило запросов по расписанию](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Правила оповещений, созданные любым API, будут управляться *тем же API*, а также порталом Azure. По умолчанию Azure Monitor будет продолжать использовать [устаревшие оповещение API Log Analytics](api-alerts.md) для создания любого нового правила генерации оповещений на портале Azure.

Последствия переключения предпочтений API правил запросов по расписанию скомпилированы ниже:

- Все взаимодействия, выполняемые для управления оповещениями журнала с помощью программного интерфейса, теперь должны выполняться с использованием [правил запросов по расписанию](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Дополнительные сведения см. в разделе [Управление оповещениями журнала с помощью шаблона ресурсов Azure](alerts-log.md#managing-log-alerts-using-azure-resource-template) и [Управление оповещениями журнала с помощью PowerShell, CLI или API](alerts-log.md#managing-log-alerts-using-powershell-cli-or-api).
- Любое новое правило генерации оповещений журнала, созданное на портале Azure, будет создано с использованием [правил запросов по расписанию](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) и позволит пользователям также использовать [дополнительную функциональность нового API](#Benefits-of-switching-to-new-Azure-API) через портал Azure.

Любой клиент, который хочет добровольно переключиться на новые [правила запросов по расписанию](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) и заблокировать использование из [устаревшего оповещения API Log Analytics](api-alerts.md), может сделать это, выполнив вызов PUT в указанном ниже API для переключения всех правил оповещения, связанных с определенной рабочей областью Log Analytics.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

С текстом запроса, который содержит указанный ниже JSON.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

Если переключение всех правил генерации оповещений в рабочей области Log Analytics на использование новых [правил запросов по расписанию](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) выполнено успешно, предоставляется следующий ответ.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

Пользователи также могут проверить текущее состояние вашего рабочего пространства Log Analytics и посмотреть, было ли оно переключено на использование только [правил запросов по расписанию](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Чтобы проверить, могут ли пользователи выполнить вызов GET на API, указанный ниже.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Если указанное рабочее пространство Log Analytics переключено на использование только [правил запросов по расписанию](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), тогда ответ JSON будет указан в списке, указанном ниже.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
В противном случае, если указанное рабочее пространство Log Analytics еще не переключено на использование только [правил запросов по расписанию](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules); тогда ответ JSON будет таким, как указано ниже.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения см. в статье [Оповещения журнала в Azure Monitor](alerts-unified-log.md).
- Узнайте о том, как создать [журнал оповещений в оповещениях Azure](alerts-log.md).
- Дополнительные сведения об [оповещениях Azure](../../azure-monitor/platform/alerts-overview.md).
