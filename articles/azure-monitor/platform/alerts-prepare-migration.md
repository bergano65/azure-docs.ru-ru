---
title: Обновление логических приложений & runbooks для миграции предупреждений
description: Узнайте, как изменять веб-крючки, логические приложения и runbooks, чтобы подготовиться к добровольной миграции.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: f31fcc07bed0287c2f86ca4fe52bf02a2a1d2a71
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114408"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Prepare your logic apps and runbooks for migration of classic alert rules (Подготовка приложений логики и модулей runbook для миграции классических правил генерации оповещений)

Как [было объявлено ранее,](monitoring-classic-retirement.md)классические оповещения в Azure Monitor удаляются в сентябре 2019 года (изначально был июль 2019 года). Инструмент миграции доступен на портале Azure для клиентов, которые используют классические правила оповещения и которые хотят сами инициировать миграцию.

> [!NOTE]
> В связи с задержкой внедрения миграционного инструмента дата выхода на пенсию по классической миграции предупреждений была продлена до 31 августа 2019 года с первоначально объявленной даты 30 июня 2019 года.

Если вы решите добровольно перенести классические правила оповещения на новые правила оповещения, имейте в виду, что между двумя системами существуют некоторые различия. В этой статье объясняются эти различия и то, как можно подготовиться к изменениям.

## <a name="api-changes"></a>Изменения в API

AA, которые создают и управляют`microsoft.insights/alertrules`классическими правилами оповещения () отличаются от`microsoft.insights/metricalerts`AA, которые создают и управляют новыми метрическими оповещениями (). Если сегодня вы программно создаете и управляете классическими правилами оповещения, обновите скрипты развертывания для работы с новыми AA.

Следующая таблица является отсылкой к программным интерфейсам как для классических, так и для новых оповещений:

|         |Классические оповещения  |Новые метрические оповещения |
|---------|---------|---------|
|REST API     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure CLI     | [az монитор оповещения](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [az монитор метрик оповещения](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Справочник](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |  [Справочник](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Шаблон Azure Resource Manager | [Для классических предупреждений](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Для новых метрических оповещений](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Изменения полезной нагрузки уведомлений

Формат полезной нагрузки уведомлений немного отличается между [классическими правилами оповещения](alerts-webhooks.md) и [новыми метрическими оповещениями.](alerts-metric-near-real-time.md#payload-schema) Если у вас есть какой-либо веб-крюк, приложение логики или действия runbook, которые вызваны классическими правилами оповещения, вы должны обновить эти конечные точки уведомления, чтобы принять формат полезной нагрузки новых метрических оповещений.

Используйте следующую таблицу для отображения полей полезной нагрузки webhook от классического формата до нового:

|  |Классические оповещения  |Новые метрические оповещения |
|---------|---------|---------|
|Было ли предупреждение активировано или устранено?    | **состояние**       | **data.status** |
|Контекстная информация о оповещении     | **context**        | **data.context**        |
|Штамп времени, на котором оповещение было активировано или решено     | **context.timestamp**       | **data.context.timestamp**        |
| Идентификатор правила оповещения | **context.id** | **data.context.id** |
| Имя правила генерации оповещений | **context.name** | **data.context.name** |
| Описание правила оповещения | **контекст.описание** | **data.context.description** |
| Состояние правила оповещения | **контекст.условие** | **data.context.condition.** |
| Имя метрики | **контекст.условие.metricName** | **data.context.condition.allOf.metricName** |
| Агрегация времени (как агрегируется метрика по окну оценки)| **context.condition.timeАгрегация** | **context.condition.timeАгрегация** |
| Период оценки | **context.condition.windowРазмер** | **data.context.condition.windowРазмер** |
| Оператор (как агрегированное метриче-значение сопожат с пороговым) | **context.condition.operator** | **data.context.condition.operator** |
| Порог | **context.condition.threshold** | **data.context.condition.allOf.threshold** |
| Метрическое значение | **context.condition.metricValue** | **data.context.condition.allOf.metricValue** |
| Идентификатор подписки | **context.subscriptionId** | **data.context.subscriptionId** |
| Ресурсная группа пострадавшего ресурса | **context.resourceGroup** | **data.context.resourceGroup** |
| Имя пострадавшего ресурса | **контекст.ресурсИмя** | **data.context.resourceName** |
| Тип пострадавшего ресурса | **context.resourceType** | **data.context.resourceType** |
| Идентификатор ресурса пострадавшего ресурса | **context.resourceId** | **data.context.resourceId** |
| Прямая ссылка на страницу сводки ресурса портала | **контекст.portalLink** | **data.context.portalLink** |
| Пользовательские поля полезной нагрузки, которые будут переданы в приложение webhook или logic | **Вариантов размещения** | **data.properties** |

Полезная нагрузка аналогична, как вы можете видеть. Следующий раздел предлагает:

- Подробная информация об изменении логических приложений для работы с новым форматом.
- Пример runbook, который разбирает полезную нагрузку уведомлений для новых оповещений.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Изменение приложения логики для получения уведомления о метике

Если вы используете логические приложения с классическими оповещениями, необходимо изменить код приложения логики, чтобы разобрать новую полезную нагрузку метрика. Выполните следующие действия.

1. Создайте новое приложение логики.

1. Используйте шаблон "Azure Monitor - Обработчик метеоля". Этот шаблон имеет триггер **запроса HTTP** с соответствующей схемой определены.

    ![логика-приложение-шаблон](media/alerts-migration/logic-app-template.png "Шаблон метеометрического оповещения")

1. Добавьте действие для размещения логики обработки.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Используйте запуск системы автоматизации, который получает уведомление о метике

Следующий пример предоставляет код PowerShell для использования в вашем runbook. Этот код может разбирать полезные нагрузки как для классических правил предупреждения метрики, так и для новых правил предупреждения метрики.

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

Полный пример runbook, который останавливает виртуальную машину при срабатывании оповещения, [см.](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)

## <a name="partner-integration-via-webhooks"></a>Интеграция партнеров через веб-крючки

Большинство [наших партнеров, которые интегрируются с классическими оповещениями,](https://docs.microsoft.com/azure/azure-monitor/platform/partners) уже поддерживают новые метрические оповещения через их интеграцию. Известные интеграции, которые уже работают с новыми метрическими оповещениями:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Если вы используете интеграцию партнеров, которая не указана здесь, подтвердите с поставщиком интеграции, что интеграция работает с новыми метрическими оповещениями.

## <a name="next-steps"></a>Дальнейшие действия

- [Как использовать средство миграции](alerts-using-migration-tool.md)
- [Принцип работы средства миграции](alerts-understand-migration.md)
