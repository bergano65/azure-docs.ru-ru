---
title: Пересылка данных задания службы автоматизации Azure в журналы Azure Monitor
description: В этой статье показано, как отправлять состояние задания и runbook потоков заданий в журналы Azure Monitor для получения дополнительных сведений и управления.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8672bc28ea5e8562472408810a38ea0de6778cfd
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65200618"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Пересылка состояния задания и потоков заданий из службы автоматизации для журналов Azure Monitor

Служба автоматизации может отправлять состояние задания runbook и потоки заданий в рабочую область Log Analytics. Этот процесс не предполагает связывания с рабочей областью и является полностью независимым. На портале Azure или с помощью PowerShell можно просмотреть журналы заданий и потоки заданий для отдельных заданий. Это дает возможность выполнять простые исследования. Теперь с помощью журналов Azure Monitor вы можете:

* получить информацию о заданиях службы автоматизации;
* активировать отправку электронного сообщения или оповещения в соответствии с состоянием задания runbook (например, сбой или приостановка);
* создавать сложные запросы для потоков заданий;
* коррелировать задания и учетные записи службы автоматизации;
* визуализировать журнал заданий по прошествии времени.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Предварительные требования и рекомендации по развертыванию

Чтобы начать отправку журналов службы автоматизации в журналы Azure Monitor, вам потребуется:

* В последнем выпуске [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* Рабочая область Log Analytics. Дополнительные сведения см. в разделе [приступить к работе с журналами Azure Monitor](../log-analytics/log-analytics-get-started.md). 
* ResourceId для учетной записи службы автоматизации Azure.

Вот как можно найти ResourceId для учетной записи службы автоматизации Azure.

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Чтобы узнать ResourceId для рабочей области Log Analytics, выполните следующую команду PowerShell.

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Если у вас несколько учетных записей службы автоматизации или рабочих областей, в выходных данных предыдущей команды найдите нужное значение *Name* и скопируйте соответствующее значение *ResourceId*.

Если вам нужно найти значение *Name* для своей учетной записи службы автоматизации, на портале Azure выберите свою учетную запись службы автоматизации в колонке **Учетная запись службы автоматизации** и выберите **Все параметры**. В колонке **Все параметры** в разделе **Параметры учетной записи** выберите пункт **Свойства**.  В колонке **Свойства** вы увидите нужные значения.<br> ![Свойства учетной записи службы автоматизации](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Настройка интеграции журналов Azure Monitor

1. На своем компьютере запустите **Windows PowerShell** на **начальном** экране.
2. Выполните приведенные ниже команды PowerShell и измените значения параметров `[your resource id]` и `[resource id of the log analytics workspace]`, указав значения из предыдущего шага.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

После запуска этого сценария может занять час перед началом просмотра записей в журналах Azure Monitor новый элемент JobLogs или JobStreams.

Чтобы просмотреть журналы, выполните следующий запрос в поиске по журналам log analytics: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Проверка конфигурации

Чтобы убедиться, что ваша учетная запись службы автоматизации отправляет журналы в рабочую область Log Analytics, проверьте правильность настройки диагностики в учетной записи службы автоматизации, используя следующую команду PowerShell.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

В выходных данных убедитесь в следующем.

* В разделе *Logs* значение *Enabled* равно *True*.
* *WorkspaceId* присвоено значение ResourceId рабочей области Log Analytics.

## <a name="azure-monitor-log-records"></a>Записи журнала Azure Monitor

О диагностике из службы автоматизации Azure создает два типа записей в журналах Azure Monitor и помечаются как **AzureDiagnostics**. В следующих запросах используется обновленный язык запросов к журналам Azure Monitor. Сведения об общих запросах между прежний язык запросов и новый язык запросов Azure Kusto [прежних версий на новый язык запросов Kusto Azure Памятка](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language)

### <a name="job-logs"></a>Журналы заданий

| Свойство | ОПИСАНИЕ |
| --- | --- |
| TimeGenerated |Дата и время выполнения задания Runbook. |
| RunbookName_s |Имя Runbook. |
| Caller_s |Сторона, инициировавшая операцию. Допустимые значения: электронный адрес или system для запланированных заданий. |
| Tenant_g | GUID, идентифицирующий клиента для вызывающего объекта. |
| JobId_g |GUID, представляющий собой идентификатор задания Runbook. |
| ResultType |Состояние задания Runbook. Возможные значения:<br>Новое<br>Started<br>- Остановлена<br>Приостановлено<br>Сбой<br>Завершено |
| Category | Классификация типа данных. Для службы автоматизации значением является JobLogs. |
| OperationName | Указывает тип операции, выполняемой в Azure. Для службы автоматизации значением является Job. |
| Resource | Имя учетной записи службы автоматизации |
| SourceSystem | Как Azure Monitor журналы сбора данных. Всегда имеет значение *Azure* для системы диагностики Azure. |
| ResultDescription |Описывает состояние результата задания Runbook. Возможные значения:<br>— Job is started;<br>— Job Failed;<br>- Job Completed. |
| CorrelationId |GUID, представляющий собой идентификатор корреляции задания Runbook. |
| ResourceId |Указывает идентификатор ресурса учетной записи службы автоматизации Azure для модуля Runbook. |
| SubscriptionId | Идентификатор подписки Azure (GUID) для учетной записи службы автоматизации. |
| ResourceGroup | Имя группы ресурсов для учетной записи службы автоматизации. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Потоки заданий
| Свойство | ОПИСАНИЕ |
| --- | --- |
| TimeGenerated |Дата и время выполнения задания Runbook. |
| RunbookName_s |Имя Runbook. |
| Caller_s |Сторона, инициировавшая операцию. Допустимые значения: электронный адрес или system для запланированных заданий. |
| StreamType_s |Тип потока задания. Возможные значения:<br>ход выполнения<br>Выходные данные<br>Предупреждение<br>error<br>debug<br>- Verbose. |
| Tenant_g | GUID, идентифицирующий клиента для вызывающего объекта. |
| JobId_g |GUID, представляющий собой идентификатор задания Runbook. |
| ResultType |Состояние задания Runbook. Возможные значения:<br>- In Progress |
| Category | Классификация типа данных. Для службы автоматизации значением является JobStreams. |
| OperationName | Указывает тип операции, выполняемой в Azure. Для службы автоматизации значением является Job. |
| Resource | Имя учетной записи службы автоматизации |
| SourceSystem | Как Azure Monitor журналы сбора данных. Всегда имеет значение *Azure* для системы диагностики Azure. |
| ResultDescription |Включает в себя выходной поток из Runbook. |
| CorrelationId |GUID, представляющий собой идентификатор корреляции задания Runbook. |
| ResourceId |Указывает идентификатор ресурса учетной записи службы автоматизации Azure для модуля Runbook. |
| SubscriptionId | Идентификатор подписки Azure (GUID) для учетной записи службы автоматизации. |
| ResourceGroup | Имя группы ресурсов для учетной записи службы автоматизации. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Просмотр журналов службы автоматизации в журналах Azure Monitor

Теперь, когда вы начали отправку журналов заданий службы автоматизации в Azure Monitor журналы, давайте посмотрим, что делать с помощью этих журналов в журналы Azure Monitor.

Чтобы просмотреть журналы, выполните следующий запрос: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Отправка электронного сообщения при сбое или приостановке задания Runbook
Один из наших основных клиентов запрашивает возможность отправлять электронное сообщение или текст при возникновении ошибки в работе задания runbook.   

Чтобы создать правило генерации оповещений, начните с создания поиска в журнале записей заданий Runbook, которые должны вызывать оповещение. Щелкните кнопку **Оповещение**, чтобы создать и настроить правило генерации оповещений.

1. На странице обзора рабочей области Log Analytics, щелкните **Просмотр журналов**.
2. Поищите по журналам свое оповещение при помощи следующего поискового запроса: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`  Вы также можете использовать группирование по RunbookName с помощью `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`.

   Если вы настроили для рабочей области журналы из более чем одной учетной записи службы автоматизации или подписки, то можете группировать оповещения по подписке или учетной записи службы автоматизации. Имя учетной записи службы автоматизации можно найти в поле "Ресурс" для поиска JobLogs.
3. Чтобы открыть экран **Создать правило**, щелкните **+ Новое правило генерации оповещений** в верхней части страницы. Дополнительные сведения о параметрах настройки оповещения см. в статье [Оповещения журнала в Azure Monitor. Интерфейс оповещений](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Поиск всех заданий, завершенных с ошибками
Помимо оповещений о сбоях можно узнать, когда задание Runbook вызывает устранимую ошибку. В этих случаях PowerShell создает поток сообщений об ошибках, но устранимые ошибки не приводят к приостановке или сбою задания.    

1. В рабочей области Log Analytics, щелкните **журналы**.
2. В поле запроса введите `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` и нажмите кнопку **Поиск**.

### <a name="view-job-streams-for-a-job"></a>Просмотр потоков заданий для задания
При отладке задания можно также просмотреть потоки заданий. Приведенный ниже запрос отображает все потоки для одного задания с GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0.   

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Просмотр хронологии состояния задания
Наконец, вам может понадобиться визуализировать журнал задания по прошествии времени. Для поиска для поиска состояния заданий по прошествии времени можно использовать приведенный ниже запрос.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`  
<br> ![Диаграмма хронологии состояния задания в Log Analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Удаление параметров диагностики

Чтобы удалить параметр диагностики из учетной записи службы автоматизации, выполните следующие команды:

```powershell-interactive
$automationAccountId = "[resource id of your automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Сводка

Отправляя данные состояния и поток задания службы автоматизации в Azure Monitor журналы, вы можете получить более глубокое представление о состоянии заданий службы автоматизации с:
+ настроить оповещения, уведомляющие вас о проблемах;
+ с помощью пользовательских представлений и поисковых запросов визуализировать результаты модуля Runbook, состояние задания Runbook и другие связанные ключевые индикаторы или метрики.  

Журналы Azure Monitor предоставляет больший оперативный контроль над заданиями службы автоматизации и поможет быстрее реагировать на инциденты.  

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о том, как создавать различные поисковые запросы и просматривать журналы заданий службы автоматизации с помощью журналов Azure Monitor, см. в разделе [при поиске по журналам в Azure Monitor журналы](../log-analytics/log-analytics-log-searches.md).
* Чтобы понять, как создавать и извлекать выходные данные и сообщения об ошибках из модулей runbook, ознакомьтесь с разделом [Выходные данные и сообщения Runbook в службе автоматизации Azure](automation-runbook-output-and-messages.md).
* Дополнительные сведения о выполнении модулей Runbook, отслеживании заданий модуля Runbook и других технических деталях см. в статье [Выполнение модуля Runbook в службе автоматизации Azure](automation-runbook-execution.md).
* Дополнительные сведения о журналах Azure Monitor и источниках собираемых данных см. в разделе [данные в хранилище Azure, сбор в Azure Monitor регистрирует Обзор](../azure-monitor/platform/collect-azure-metrics-logs.md).

