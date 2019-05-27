---
title: Подготовка к миграции классических оповещений Azure Monitor, обновив logic apps и модулей Runbook
description: Узнайте, как изменить веб-перехватчики, приложения логики и модулей Runbook для подготовки к миграции добровольное.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: bdbd45c2b10dec8f1c0a85110747a470e818dbf9
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2019
ms.locfileid: "66015610"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Подготовка приложений логики и модулей Runbook для миграции классической правил генерации оповещений

Как [было объявлено ранее](monitoring-classic-retirement.md), классических оповещений в Azure Monitor будет прекращена в сентябре 2019 (был изначально июля 2019 г). Средства миграции доступен на портале Azure клиентам, кто использовать классические правила генерации оповещений, которые хотят активировать миграции сами.

> [!NOTE]
> Из-за задержки в развертывание средства миграции даты прекращения использования для переноса классических оповещений расширен для 31 августа 2019 г. с изначально было объявлено даты 30 июня 2019 г.

Если вы решили добровольно перенести классические правила генерации оповещения для нового правила генерации оповещений, имейте в виду, что существуют некоторые различия между двумя системами. Эта статья объясняет, эти различия и как вы можете подготовить для изменения.

## <a name="api-changes"></a>Изменения в API

Интерфейсы API, создавать и управлять ими в классическом правила генерации оповещений (`microsoft.insights/alertrules`) отличаются от API, создавать и управлять ими новые оповещения метрик (`microsoft.insights/metricalerts`). Если программное создание и управление сегодня классические правила генерации оповещений, обновите скрипты развертывания для работы с помощью новых интерфейсов API.

Следующая таблица представляет ссылку на программные интерфейсы для классических и новых оповещений:

|         |Классические оповещения  |Новые оповещения метрик |
|---------|---------|---------|
|REST API     | [имеет значение Microsoft.Insights/alertrules.](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [Microsoft.Insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Инфраструктура CLI Azure     | [Предупреждение монитора AZ](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [оповещение о метриках AZ monitor](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Справочные материалы](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |  [Справочные материалы](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Шаблон Azure Resource Manager | [Для классических оповещений](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Для новых оповещений метрик](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Изменения полезные данные уведомления

Формат полезных данных уведомления зависит от выбора [классические правила генерации оповещений](alerts-webhooks.md) и [новые оповещения метрик](alerts-metric-near-real-time.md#payload-schema). При наличии любой веб-перехватчик, приложение логики или действий runbook, которые происходят в классическом правилами генерации оповещений, необходимо обновить этих конечных точек уведомления, чтобы принять формат полезных данных новых оповещений метрик.

Используйте следующую таблицу для отображения полей полезных данных веб-перехватчика из классической формата в новый формат:

|  |Классические оповещения  |Новые оповещения метрик |
|---------|---------|---------|
|Оповещение или активации разрешения?    | **состояние**       | **data.status** |
|Контекстные сведения об оповещении     | **контекста**        | **Data.Context**        |
|Метка времени, по которому предупреждение или активации разрешения     | **context.timestamp**       | **data.context.timestamp**        |
| Идентификатор правила генерации оповещений | **context.ID** | **Data.Context.ID** |
| Имя правила генерации оповещений | **context.Name** | **Data.Context.Name** |
| Описание правила оповещения | **context.Description** | **Data.Context.Description** |
| Условие правила генерации оповещений | **context.Condition** | **Data.Context.Condition** |
| Название метрики | **context.condition.metricName** | **data.context.condition.allOf[0].metricName** |
| Агрегат времени (как метрики агрегируются поверх окна оценки)| **data.context.condition.timeAggregation** | **data.context.condition.timeAggregation** |
| Период оценки | **context.condition.windowSize** | **data.context.condition.windowSize** |
| Оператор (как объединенные метрики значение сравнивается с пороговым значением) | **context.Condition.operator** | **Data.Context.Condition.operator** |
| Threshold (Пороговое значение) | **context.Condition.threshold** | **data.context.condition.allOf[0].threshold** |
| Значение метрики | **context.condition.metricValue** | **data.context.condition.allOf[0].metricValue** |
| Идентификатор подписки | **context.subscriptionId** | **data.context.subscriptionId** |
| Группы ресурсов затронутого ресурса | **context.resourceGroup** | **data.context.resourceGroup** |
| Имя затронутого ресурса | **context.resourceName** | **data.context.resourceName** |
| Тип затронутого ресурса | **context.resourceType** | **data.context.resourceType** |
| Идентификатор ресурса затронутого ресурса | **context.resourceId** | **data.context.resourceId** |
| Прямая ссылка на страницу сводки ресурса на портале | **context.portalLink** | **data.context.portalLink** |
| Настраиваемые полезные данные поля должны быть переданы на веб-перехватчик или логику приложения | **properties** | **Data.Properties** |

Полезные данные похожи друг от друга, как вы видите. Предоставляет следующий раздел:

- Сведения об изменении приложения логики для работы с новым форматом.
- Это пример runbook, который анализирует полезные данные уведомления о новых оповещениях.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Изменение приложения логики для получения метрик уведомления о предупреждении

Если вы используете logic apps с помощью классических оповещений, необходимо изменить код приложения логики, чтобы проанализировать полезные данные нового оповещения на основе метрик. Выполните следующие действия.

1. Создайте новое приложение логики.

1. Используйте шаблон «Монитор — метрики оповещений обработчик Azure». Этот шаблон содержит **HTTP-запроса** триггера с соответствующей схемой определен.

    ![Шаблон приложения логики](media/alerts-migration/logic-app-template.png "метрик шаблона оповещения")

1. Добавление действия для размещения логики обработки.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Используйте модуль runbook службы автоматизации, получающий метрик уведомления о предупреждении

Следующий пример содержит код PowerShell для использования в модуле runbook. Этот код можно проанализировать полезные данные для классического правил генерации оповещений метрик и новых правил генерации оповещений метрик.

```PowerShell
## Example PowerShell code to use in a runbook to handle parsing of both classic and new metric alerts.

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData.
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Determine whether the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new metric alert schema.
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic metric alert schema.
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is neither a classic metric alert nor a new metric alert.
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    # Parse fields related to resource affected.
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

Полный пример модуля runbook, который останавливает виртуальную машину при активации оповещения, см. в разделе [документации по службе автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Интеграция партнеров с помощью объектов webhook

Большая часть [наших партнеров, которые интегрируются с классических оповещений](https://docs.microsoft.com/azure/azure-monitor/platform/partners) уже поддерживают новые оповещения метрик, путем их интеграций. Приведены известные интеграций, которые уже работают с нового оповещения на основе метрик.

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

При использовании интеграции партнеров, который отсутствует в списке ниже, подтвердите с поставщиком интеграции что интеграции работает с нового оповещения на основе метрик.

## <a name="next-steps"></a>Дальнейшие действия

- [Как использовать средство миграции](alerts-using-migration-tool.md)
- [Понять, как работает средство миграции](alerts-understand-migration.md)
