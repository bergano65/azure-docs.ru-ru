---
title: Подготовка к миграции классических оповещений Azure Monitor, обновив logic apps и модулей Runbook
description: Узнайте, как изменить веб-перехватчика, приложение логики и модули Runbook для подготовки к миграции добровольное.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 3c47404826d5055d4a82d4842523f790fb11f000
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632516"
---
# <a name="prepare-your-logic-apps-and-run-books-for-classic-alert-rules-migration"></a>Подготовка приложений логики и запуска книги для миграции классической правила генерации оповещений

Как [было объявлено ранее](monitoring-classic-retirement.md), классических оповещений в Azure Monitor, прекращается в июля 2019 г. Средство миграции для запуска миграции добровольно доступна на портале Azure и станет доступен для клиентов, использующих классические правила генерации оповещений.

Если вы решили добровольно перенести классические правила генерации оповещения для нового правила генерации оповещений, существуют некоторые различия между двумя системами, которые следует учитывать. В этой статье пошагово продемонстрирует различия между двумя системами и как вы можете подготовить для изменения.

## <a name="api-changes"></a>Изменения в API

API, используемые для создания и управления классические правила генерации оповещений (`microsoft.insights/alertrules`) отличаются от API, используемые для создания и управления новых оповещений метрик (`microsoft.insights/metricalerts`). Если вы программным образом создать и настроить классические правила генерации оповещений сегодня, обновите скрипты развертывания для работы с помощью новых API.

Следующая таблица предоставляет ссылку на программные интерфейсы для классических и новых оповещений.

|         |Классические оповещения  |Новые оповещения метрик |
|---------|---------|---------|
|REST API     | [имеет значение Microsoft.Insights/alertrules.](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [Microsoft.Insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Инфраструктура CLI Azure     | [Предупреждение монитора AZ](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [оповещение о метриках AZ monitor](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Справочные материалы](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |      |
| Шаблон Azure Resource Manager | [Для классических оповещений](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Для новых оповещений метрик](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Изменения полезные данные уведомления

Формат полезных данных уведомления зависит от выбора [классические правила генерации оповещений](alerts-webhooks.md) и [новые оповещения метрик](alerts-metric-near-real-time.md#payload-schema). Если у вас есть какие-либо веб-перехватчик, приложение логики или активируется классические правила генерации оповещений действия runbook, необходимо будет обновить этих конечных точек уведомления, чтобы принять формат полезных данных новых оповещений метрик.

В следующей таблице можно использовать для сопоставления полей между полезные данные веб-перехватчика классическое правило генерации оповещений и новых полезных данных webhook для оповещения метрики.

|  |Классические оповещения  |Новые оповещения метрик |
|---------|---------|---------|
|Оповещение или активации разрешения     | status       | data.status |
|Контекстные сведения об оповещении     | context        | data.context        |
|Метка времени, по которому предупреждение или активации разрешения      | context.timestamp       | Data.Context.timestamp        |
| Идентификатор правила генерации оповещений | context.ID | Data.Context.ID |
| Имя правила генерации оповещений | context.Name | Data.Context.Name |
| Описание правила оповещения | context.Description | Data.Context.Description |
| Условие правила генерации оповещений | context.Condition | Data.Context.Condition|
| Имя метрики | context.condition.metricName| data.context.condition.allOf[0].metricName|
| Агрегат времени (как метрики агрегируются поверх окна оценки)|data.context.condition.timeAggregation|data.context.condition.timeAggregation|
| Период оценки | context.condition.windowSize | data.context.condition.windowSize|
| Оператор (как объединенные метрики значение сравнивается с пороговым значением) | context.Condition.operator | Data.Context.Condition.operator|
| Threshold (Пороговое значение) | context.Condition.threshold| data.context.condition.allOf[0].threshold|
| Значение метрики | context.condition.metricValue | data.context.condition.allOf[0].metricValue|
| Идентификатор подписки | context.subscriptionId | data.context.subscriptionId|
| Группы ресурсов затронутого ресурса | context.resourceGroup | data.context.resourceGroup|
| Имя затронутого ресурса | context.resourceName | data.context.resourceName |
| Тип затронутого ресурса | context.resourceType | data.context.resourceType |
|  Идентификатор ресурса затронутого ресурса | context.resourceId | data.context.resourceId |
| Прямая ссылка на страницу сводки ресурса на портале | context.portalLink | data.context.portalLink|
| Настраиваемые полезные данные поля должны быть переданы на веб-перехватчика или приложения логики | properties |Data.Properties |

Как вы видите, как полезные данные похожи. Следующем разделе содержит сведения о примеры приложений логики и пример модуля runbook, чтобы проанализировать полезные данные уведомления о новых оповещениях.

## <a name="using-a-logic-app-that-receives-a-metric-alert-notification"></a>С помощью приложения логики, которое получает метрики уведомления о предупреждении

Если вы используете приложения логики с помощью классических оповещений, необходимо изменить приложение логики, чтобы проанализировать полезные данные нового оповещения на основе метрик.

1. Создайте новое приложение логики.

2. Используйте шаблон «Монитор — метрики оповещений обработчик Azure». Этот шаблон содержит **HTTP-запроса** триггера с соответствующей схемой определенные

    ![Шаблон приложения логики](media/alerts-migration/logic-app-template.png "метрик шаблона оповещения")

3. Добавление действия для размещения логики обработки.

## <a name="using-an-automation-runbook-that-receives-a-metric-alert-notification"></a>С помощью runbook службы автоматизации, получающий метрик уведомления о предупреждении

Следующий пример содержит код PowerShell, который может использоваться в модуле runbook, который может выполнять синтаксический анализ полезных данных классического правил генерации оповещений метрик и новых правил генерации оповещений метрик.

```PS
## Sample PowerShell code to be used in a runbook to handle parsing of both classic and new metric alerts

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

    # Identify if the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new Metric Alert schema
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic Metric Alert schema
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is not either a classic metric alert or a new metric alert
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    #parse fields related to resource affected
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

См. Полный пример модуля runbook, который останавливает виртуальную Машину при активации оповещения в [документации по службе автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Интеграция партнеров с помощью объектов webhook

Большая часть [наших партнеров, которые интегрируются с классических оповещений](https://docs.microsoft.com/azure/azure-monitor/platform/partners) уже поддерживают новые оповещения метрик, путем их интеграций. Ниже перечислены известные интеграций, которые уже работают с нового оповещения на основе метрик.

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Если вы используете здесь Интеграция партнеров, который отсутствует в списке, убедитесь, с поставщиком интеграции что интеграции работает с нового оповещения на основе метрик.

## <a name="next-steps"></a>Дальнейшие действия

- [Как использовать средство миграции](alerts-using-migration-tool.md)
- [Понять, как работает средство миграции](alerts-understand-migration.md)
