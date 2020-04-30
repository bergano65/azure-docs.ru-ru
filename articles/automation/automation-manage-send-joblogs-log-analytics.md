---
title: Пересылка данных задания службы автоматизации Azure в журналы Azure Monitor
description: В этой статье показано, как отправить данные о состоянии задания и потоки заданий Runbook в журналы Azure Monitor для предоставления дополнительных сведений и управления.
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: a9f4e641e60d6cf1c481c445767422e8b4df683b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457694"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Пересылка состояния задания и потоков заданий из службы автоматизации в журналы Azure Monitor

Служба автоматизации может отправлять состояние задания runbook и потоки заданий в рабочую область Log Analytics. Этот процесс не предполагает связывания с рабочей областью и является полностью независимым. На портале Azure или с помощью PowerShell можно просмотреть журналы заданий и потоки заданий для отдельных заданий. Это дает возможность выполнять простые исследования. Теперь с Azure Monitor журналами можно:

* Получите представление о состоянии заданий службы автоматизации.
* активировать отправку электронного сообщения или оповещения в соответствии с состоянием задания runbook (например, сбой или приостановка);
* создавать сложные запросы для потоков заданий;
* коррелировать задания и учетные записи службы автоматизации;
* Используйте пользовательские представления и поисковые запросы для визуализации результатов Runbook, состояния задания Runbook и других связанных ключевых показателей или метрик.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке модуля Az в гибридной рабочей роли Runbook см. в статье об [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Чтобы обновить модули в учетной записи службы автоматизации, см. руководство по [обновлению модулей Azure PowerShell в службе автоматизации Azure](automation-update-azure-modules.md).

## <a name="prerequisites-and-deployment-considerations"></a>Предварительные требования и рекомендации по развертыванию

Чтобы начать отправку журналов службы автоматизации в журналы Azure Monitor, вам потребуется:

* Последний выпуск [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* Рабочая область Log Analytics. Дополнительные сведения см. в статье [Приступая к работе с журналами Azure Monitor](../log-analytics/log-analytics-get-started.md).
* Идентификатор ресурса для учетной записи службы автоматизации Azure.

Чтобы найти идентификатор ресурса для учетной записи службы автоматизации Azure, используйте следующую команду:

```powershell-interactive
# Find the ResourceId for the Automation account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Чтобы найти идентификатор ресурса для рабочей области Log Analytics, выполните следующую команду PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Если в выходных данных предыдущих команд имеется более одной учетной записи или рабочей области службы автоматизации, найдите имя, которое необходимо настроить, и скопируйте значение для идентификатора ресурса.

1. В портал Azure выберите учетную запись службы автоматизации в колонке **учетная запись службы автоматизации** и выберите **все параметры**. 
2. В колонке **все параметры** в разделе **Параметры учетной записи**выберите **Свойства**.  
3. В колонке **Свойства** Обратите внимание на свойства, показанные ниже.

    ![Свойства учетной записи службы автоматизации](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="azure-monitor-log-records"></a>Записи журнала Azure Monitor

Система диагностики службы автоматизации Azure создает в Azure Monitor журналах два типа записей, `AzureDiagnostics`помеченные как. Таблицы в следующих разделах представляют собой примеры записей, создаваемых службой автоматизации Azure, и типов данных, которые отображаются в результатах поиска по журналам.

### <a name="job-logs"></a>Журналы заданий

| Свойство | Описание |
| --- | --- |
| TimeGenerated |Дата и время выполнения задания Runbook. |
| RunbookName_s |Имя Runbook. |
| Caller_s |Вызывающий объект, инициировавший операцию. Допустимые значения: электронный адрес или system для запланированных заданий. |
| Tenant_g | Идентификатор GUID, определяющий клиента для вызывающего объекта. |
| JobId_g |Идентификатор GUID, определяющий задание Runbook. |
| ResultType |Состояние задания Runbook. Возможны следующие значения:<br>Новое<br>— Создано<br>Started<br>- Остановлена<br>Приостановлено<br>Сбой<br>Завершено |
| Категория | Классификация типа данных. Для службы автоматизации значением является JobLogs. |
| OperationName | Тип операции, выполняемой в Azure. Для службы автоматизации значением является Job. |
| Ресурс | Имя учетной записи службы автоматизации |
| SourceSystem | Система, в которой Azure Monitor журналы используются для получения данных. Это значение всегда является Azure для системы диагностики Azure. |
| ResultDescription |Состояние результата задания Runbook. Возможны следующие значения:<br>— Job is started;<br>— Job Failed;<br>- Job Completed. |
| CorrelationId |Идентификатор GUID корреляции задания Runbook. |
| ResourceId |Идентификатор ресурса учетной записи службы автоматизации Azure для модуля Runbook. |
| SubscriptionId | Идентификатор GUID подписки Azure для учетной записи службы автоматизации. |
| ResourceGroup | Имя группы ресурсов для учетной записи службы автоматизации. |
| ResourceProvider | Поставщики ресурсов. Значение — MICROSOFT. Средствами. |
| ResourceType | Тип ресурса. Значение равно AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Потоки заданий
| Свойство | Описание |
| --- | --- |
| TimeGenerated |Дата и время выполнения задания Runbook. |
| RunbookName_s |Имя Runbook. |
| Caller_s |Вызывающий объект, инициировавший операцию. Допустимые значения: электронный адрес или system для запланированных заданий. |
| StreamType_s |Тип потока задания. Возможны следующие значения:<br>ход выполнения<br>Выходные данные<br>Предупреждение<br>error<br>debug<br>- Verbose. |
| Tenant_g | Идентификатор GUID, определяющий клиента для вызывающего объекта. |
| JobId_g |Идентификатор GUID, определяющий задание Runbook. |
| ResultType |Состояние задания Runbook. Возможны следующие значения:<br>- In Progress |
| Категория | Классификация типа данных. Для службы автоматизации значением является JobStreams. |
| OperationName | Тип операции, выполняемой в Azure. Для службы автоматизации значением является Job. |
| Ресурс | Имя учетной записи службы автоматизации. |
| SourceSystem | Система, в которой Azure Monitor журналы используются для получения данных. Это значение всегда является Azure для системы диагностики Azure. |
| ResultDescription |Описание, включающее выходной поток из модуля Runbook. |
| CorrelationId |Идентификатор GUID корреляции задания Runbook. |
| ResourceId |Идентификатор ресурса учетной записи службы автоматизации Azure для модуля Runbook. |
| SubscriptionId | Идентификатор GUID подписки Azure для учетной записи службы автоматизации. |
| ResourceGroup | Имя группы ресурсов для учетной записи службы автоматизации. |
| ResourceProvider | Поставщики ресурсов. Значение — MICROSOFT. Средствами. |
| ResourceType | Тип ресурса. Значение равно AUTOMATIONACCOUNTS. |

## <a name="setting-up-integration-with-azure-monitor-logs"></a>Настройка интеграции с журналами Azure Monitor

1. На своем компьютере запустите Windows PowerShell на **начальном** экране.
2. Выполните следующие команды PowerShell и измените значения для `[your resource ID]` и `[resource ID of the log analytics workspace]` на значения из предыдущего раздела.

   ```powershell-interactive
   $workspaceId = "[resource ID of the log analytics workspace]"
   $automationAccountId = "[resource ID of your Automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

После выполнения этого скрипта может пройти час, прежде чем начать просмотр записей в Azure Monitor журналов новых `JobLogs` или `JobStreams` записанных.

Чтобы просмотреть журналы, выполните следующий запрос в поиске журналов log Analytics:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Проверка конфигурации

Чтобы убедиться, что ваша учетная запись службы автоматизации отправляет журналы в рабочую область Log Analytics, проверьте, правильно ли настроена диагностика в учетной записи службы автоматизации, используя следующую команду PowerShell.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

В выходных данных убедитесь, что:

* В `Logs`поле значение `Enabled` равно true.
* `WorkspaceId`задается `ResourceId` значение для рабочей области log Analytics.

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Просмотр журналов службы автоматизации в журналах Azure Monitor

Теперь, когда вы начали отправлять журналы заданий службы автоматизации в журналы Azure Monitor, давайте посмотрим, что можно делать с этими журналами в Azure Monitor журналах.

Чтобы просмотреть журналы, выполните следующий запрос: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Отправка электронного сообщения при сбое или приостановке задания Runbook

Один из наших основных клиентов запрашивает возможность отправлять электронное сообщение или текст при возникновении ошибки в работе задания runbook.

Чтобы создать правило генерации оповещений, начните с создания поиска по журналам для записей заданий Runbook, которые должны вызывать предупреждение. Щелкните кнопку **Оповещение**, чтобы создать и настроить правило генерации оповещений.

1. На странице обзора рабочей области Log Analytics щелкните **Просмотр журналов**.
2. Создайте запрос поиска по журналам для оповещения, введя следующий поиск в поле запроса:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>Также можно выполнить группировку по имени Runbook с помощью:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Если вы настроили для рабочей области журналы из более чем одной учетной записи службы автоматизации или подписки, то можете группировать оповещения по подписке или учетной записи службы автоматизации. Имя учетной записи службы автоматизации можно найти `Resource` в поле поиска `JobLogs`.
3. Чтобы открыть экран **Создание правила** , щелкните **новое правило генерации оповещений** в верхней части страницы. Дополнительные сведения о параметрах настройки оповещения см. в статье [Оповещения журнала в Azure Monitor. Интерфейс оповещений](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Поиск всех заданий, завершенных с ошибками

Помимо оповещений о сбоях можно узнать, когда задание Runbook вызывает устранимую ошибку. В таких случаях PowerShell создает поток ошибок, но неустранимые ошибки не приводят к приостановке или сбою задания.

1. В рабочей области Log Analytics щелкните **журналы**.
2. В поле запроса введите `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`.
3. Нажмите кнопку **Search (Поиск** ).

### <a name="view-job-streams-for-a-job"></a>Просмотр потоков заданий для задания

При отладке задания может также потребоваться просмотреть потоки заданий. Приведенный ниже запрос отображает все потоки для одного задания с GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Просмотр хронологии состояния задания

Наконец, может потребоваться визуализировать историю заданий с течением времени. Для поиска для поиска состояния заданий по прошествии времени можно использовать приведенный ниже запрос.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Диаграмма хронологии состояния задания в Log Analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="removing-diagnostic-settings"></a>Удаление параметров диагностики

Чтобы удалить параметр диагностики из учетной записи службы автоматизации, выполните следующую команду:

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```
## <a name="next-steps"></a>Дальнейшие шаги

* Сведения об устранении неполадок Log Analytics см. в разделе [Устранение неполадок, почему log Analytics больше не собирает данные](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
* Дополнительные сведения о том, как создавать различные поисковые запросы и просматривать журналы заданий службы автоматизации с помощью журналов Azure Monitor, см. [в разделе Поиск по журналам в Azure Monitor журналах](../log-analytics/log-analytics-log-searches.md).
* Сведения о том, как создавать и получать выходные данные и сообщения об ошибках из модулей Runbook, см. в разделе [Runbook Output and messages](automation-runbook-output-and-messages.md).
* Дополнительные сведения о выполнении модулей Runbook, отслеживании заданий модуля Runbook и других технических деталях см. в статье [Выполнение модуля Runbook в службе автоматизации Azure](automation-runbook-execution.md).
* Дополнительные сведения о Azure Monitor журналах и источниках сбора данных см. [в разделе Сбор данных службы хранилища Azure в журналах Azure Monitor](../azure-monitor/platform/collect-azure-metrics-logs.md).