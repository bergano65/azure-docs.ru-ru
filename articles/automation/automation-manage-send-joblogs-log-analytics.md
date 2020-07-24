---
title: Пересылка данных задания службы автоматизации Azure в журналы Azure Monitor
description: В этой статье рассказывается, как отправлять данные о состоянии задания и потоки заданий runbook в журналы Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 05/22/2020
ms.topic: conceptual
ms.openlocfilehash: 2fe6cbdbcb0cf5b5c28d34f2059a2b070b059566
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87004755"
---
# <a name="forward-azure-automation-job-data-to-azure-monitor-logs"></a>Пересылка данных задания службы автоматизации Azure в журналы Azure Monitor

Служба автоматизации Azure может отправлять состояние задания runbook и потоки заданий в рабочую область Log Analytics. Этот процесс не предполагает связывания с рабочей областью и является полностью независимым. На портале Azure или с помощью PowerShell можно просмотреть журналы заданий и потоки заданий для отдельных заданий. Это дает возможность выполнять простые исследования. С помощью журналов Azure Monitor вы можете:

* получить информацию о состоянии заданий службы автоматизации;
* активировать отправку электронного сообщения или оповещения в соответствии с состоянием задания runbook (например, сбой или приостановка);
* создавать сложные запросы для потоков заданий;
* коррелировать задания и учетные записи службы автоматизации;
* с помощью пользовательских представлений и поисковых запросов визуализировать результаты выполнения runbook, состояние задания runbook и другие ключевые индикаторы или метрики.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Предварительные требования и рекомендации по развертыванию

Чтобы начать отправку журналов службы автоматизации в журналы Azure Monitor, необходимо следующее:

* Последний выпуск [Azure PowerShell](/powershell/azure/).
* Рабочая область Log Analytics. Дополнительные сведения см. в статье [Начало работы с журналами Azure Monitor](../azure-monitor/overview.md).
* Идентификатор ресурса для учетной записи службы автоматизации Azure.

Выполните следующую команду, чтобы получить идентификатор ресурса для учетной записи службы автоматизации Azure:

```powershell-interactive
# Find the ResourceId for the Automation account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Чтобы узнать идентификатор ресурса для рабочей области Log Analytics, выполните следующую команду PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Если в выходных данных предыдущих команд указано несколько учетных записей службы автоматизации или рабочих областей, вы можете выяснить имя и другие свойства для получения полного идентификатора ресурса учетной записи службы автоматизации, выполнив следующие действия:

1. На портале Azure выберите учетную запись службы автоматизации на странице **Учетные записи службы автоматизации**. 
2. На странице выбранной учетной записи службы автоматизации в разделе **Настройки учетной записи** выберите пункт **Свойства**.  
3. На странице **Свойства** найдите сведения, показанные ниже.

    ![Свойства учетной записи службы автоматизации](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="azure-monitor-log-records"></a>Записи журнала Azure Monitor

Диагностика службы автоматизации Azure создает в журналах Azure Monitor записи двух типов с тегом `AzureDiagnostics`. В таблицах следующих разделов приведены примеры записей, создаваемых службой автоматизации Azure, и типы данных, которые отображаются в результатах поиска по журналам.

### <a name="job-logs"></a>Журналы заданий

| Свойство | Описание |
| --- | --- |
| TimeGenerated |Дата и время выполнения задания Runbook. |
| RunbookName_s |Имя Runbook. |
| Caller_s |Вызывающая сторона, которая запустила эту операцию. Допустимые значения: электронный адрес или system для запланированных заданий. |
| Tenant_g | GUID, идентифицирующий клиент для вызывающего объекта. |
| JobId_g |GUID, идентифицирующий задание runbook. |
| ResultType |Состояние задания Runbook. Возможны следующие значения:<br>Новое<br>- Создано<br>Started<br>- Остановлена<br>Приостановлено<br>Сбой<br>Завершено |
| Категория | Классификация типа данных. Для службы автоматизации значением является JobLogs. |
| OperationName | Тип операции, выполняемой в Azure. Для службы автоматизации значением является Job. |
| Ресурс | Имя учетной записи службы автоматизации |
| SourceSystem | Система, которую используют журналы Azure Monitor для сбора данных. Для системы диагностики Azure здесь всегда указано значение "Azure". |
| ResultDescription |Состояние результата задания runbook. Возможны следующие значения:<br>— Job is started;<br>— Job Failed;<br>- Job Completed. |
| CorrelationId |GUID корреляции для задания runbook. |
| ResourceId |Указывает идентификатор ресурса для учетной записи службы автоматизации Azure, к которой относится этот runbook. |
| SubscriptionId | GUID подписки Azure для учетной записи службы автоматизации. |
| ResourceGroup | Имя группы ресурсов для учетной записи службы автоматизации. |
| ResourceProvider | Поставщик ресурса. Имеет значение MICROSOFT.AUTOMATION. |
| ResourceType | Тип ресурса. Имеет значение AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Потоки заданий
| Свойство | Описание |
| --- | --- |
| TimeGenerated |Дата и время выполнения задания Runbook. |
| RunbookName_s |Имя Runbook. |
| Caller_s |Вызывающая сторона, которая запустила эту операцию. Допустимые значения: электронный адрес или system для запланированных заданий. |
| StreamType_s |Тип потока задания. Возможны следующие значения:<br>ход выполнения<br>Выходные данные<br>Предупреждение<br>error<br>debug<br>- Verbose. |
| Tenant_g | GUID, идентифицирующий клиент для вызывающего объекта. |
| JobId_g |GUID, идентифицирующий задание runbook. |
| ResultType |Состояние задания Runbook. Возможны следующие значения:<br>- In Progress |
| Категория | Классификация типа данных. Для службы автоматизации значением является JobStreams. |
| OperationName | Тип операции, выполняемой в Azure. Для службы автоматизации значением является Job. |
| Ресурс | Имя учетной записи службы автоматизации. |
| SourceSystem | Система, которую используют журналы Azure Monitor для сбора данных. Для системы диагностики Azure здесь всегда указано значение "Azure". |
| ResultDescription |Описание, в котором указан выходной поток этого runbook. |
| CorrelationId |GUID корреляции для задания runbook. |
| ResourceId |Указывает идентификатор ресурса для учетной записи службы автоматизации Azure, к которой относится этот runbook. |
| SubscriptionId | GUID подписки Azure для учетной записи службы автоматизации. |
| ResourceGroup | Имя группы ресурсов для учетной записи службы автоматизации. |
| ResourceProvider | Поставщик ресурса. Имеет значение MICROSOFT.AUTOMATION. |
| ResourceType | Тип ресурса. Имеет значение AUTOMATIONACCOUNTS. |

## <a name="set-up-integration-with-azure-monitor-logs"></a>Настройка интеграции с журналами Azure Monitor

1. На своем компьютере запустите Windows PowerShell с **начального экрана**.
2. Выполните приведенные ниже команды PowerShell, указав для параметров `$automationAccountId` и `$workspaceId` значения из предыдущего раздела.

   ```powershell-interactive
   $workspaceId = "resource ID of the log analytics workspace"
   $automationAccountId = "resource ID of your Automation account"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

После запуска этого скрипта может потребоваться около часа, прежде чем в журналах Azure Monitor начнут появляться записи для новых `JobLogs` или `JobStreams`.

Чтобы просмотреть журналы, выполните следующий запрос в поиске по журналам Log Analytics: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Проверка конфигурации

Чтобы убедиться, что ваша учетная запись службы автоматизации отправляет журналы в рабочую область Log Analytics, проверьте правильность настройки диагностики в учетной записи службы автоматизации, используя следующую команду PowerShell.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

В выходных данных проверьте следующее:

* В разделе `Logs` свойство `Enabled` имеет значение True.
* `WorkspaceId` имеет значение `ResourceId` для рабочей области Log Analytics.

## <a name="view-automation-logs-in-azure-monitor-logs"></a>Просмотр журналов службы автоматизации в журналах Azure Monitor

Теперь, когда успешно запущена отправка журналов заданий службы автоматизации в журналы Azure Monitor, мы попробуем что-нибудь сделать с ними в среде журналов Azure Monitor.

Чтобы просмотреть журналы, выполните следующий запрос: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Отправка электронного сообщения при сбое или приостановке задания Runbook

В следующих шагах показано, как настроить в Azure Monitor оповещения о том, что с заданием runbook что-то пошло не так.

Чтобы создать правило генерации оповещений, для начала создайте поиск по журналам для записей заданий runbook, по которым вам требуется оповещение. Щелкните кнопку **Оповещение**, чтобы создать и настроить правило генерации оповещений.

1. На странице обзорных сведений о рабочей области Log Analytics щелкните **Просмотр журналов**.

2. Создайте запрос поиска по журналам для нужного оповещения, введя следующий текст в поле запроса: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>Вы также можете использовать группирование по имени runbook с помощью `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Если вы настроили для рабочей области журналы из более чем одной учетной записи службы автоматизации или подписки, то можете группировать оповещения по подписке или учетной записи службы автоматизации. Имя учетной записи службы автоматизации можно найти в поле `Resource` в результатах поиска `JobLogs`.

3. Чтобы открыть экран **Создание правила**, щелкните **Новое правило генерации оповещений** в верхней части страницы. Дополнительные сведения о параметрах настройки оповещения см. в статье [Оповещения журнала в Azure Monitor. Интерфейс оповещений](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Поиск всех заданий, завершенных с ошибками

Помимо оповещений о сбоях можно узнать, когда задание Runbook вызывает устранимую ошибку. В этих случаях PowerShell создает поток ошибок, но устранимые ошибки не приводят к приостановке или сбою задания.

1. В рабочей области Log Analytics щелкните **Журналы**.

2. В поле поискового запроса введите `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`.

3. Нажмите кнопку **Поиск**.

### <a name="view-job-streams-for-a-job"></a>Просмотр потоков заданий для задания

При отладке задания вам могут пригодиться потоки заданий. Приведенный ниже запрос отображает все потоки для одного задания с GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Просмотр хронологии состояния задания

Наконец, вам может понадобиться визуализировать временную линию по журналу заданий. Для поиска для поиска состояния заданий по прошествии времени можно использовать приведенный ниже запрос.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Диаграмма хронологии состояния задания в Log Analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Удаление параметров диагностики

Чтобы удалить параметр диагностики из учетной записи службы автоматизации, выполните следующую команду:

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать, как создавать поисковые запросы и просматривать журналы заданий службы автоматизации на платформе журналов Azure Monitor, ознакомьтесь со статьей [Поиск в журналах Azure Monitor](../azure-monitor/log-query/log-query-overview.md).
* Чтобы узнать больше о создании и получении выходных данных и сообщений об ошибках для runbook, см. статью [Отслеживание выходных данных runbook](automation-runbook-output-and-messages.md).
* Дополнительные сведения о выполнении runbook, отслеживании заданий runbook и другие технические подробности см. в статье [Выполнение runbook в службе автоматизации Azure](automation-runbook-execution.md).
* Чтобы узнать больше о журналах Azure Monitor и источниках сбора данных, см. статью [Общие сведения о сборе данных службы хранилища Azure на платформе журналов Azure Monitor](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).
* В устранении неполадок с Log Analytics вам может помочь раздел [Почему Log Analytics больше не собирает данные](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
