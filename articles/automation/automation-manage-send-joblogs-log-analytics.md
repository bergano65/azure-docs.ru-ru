---
title: Пересылка данных задания службы автоматизации Azure в журналы Azure Monitor
description: В этой статье показано, как отправлять сведения о состоянии задания и потоки вакансий в журналы Azure Monitor для получения дополнительной информации и управления.
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: 54f77f55a127cd712d43419eb6a85fd5d93a478c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652174"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Переработайте статус задания и потоки рабочих мест от журналов автоматизации к мониторингу Azure

Служба автоматизации может отправлять состояние задания runbook и потоки заданий в рабочую область Log Analytics. Этот процесс не предполагает связывания с рабочей областью и является полностью независимым. На портале Azure или с помощью PowerShell можно просмотреть журналы заданий и потоки заданий для отдельных заданий. Это дает возможность выполнять простые исследования. Теперь с журналами Azure Monitor вы можете:

* получить информацию о заданиях службы автоматизации;
* активировать отправку электронного сообщения или оповещения в соответствии с состоянием задания runbook (например, сбой или приостановка);
* создавать сложные запросы для потоков заданий;
* коррелировать задания и учетные записи службы автоматизации;
* визуализировать журнал заданий по прошествии времени.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Предварительные требования и рекомендации по развертыванию

Чтобы начать отправку журналов автоматизации в журналы Azure Monitor, необходимо:

* Последний релиз [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* Рабочая область Log Analytics. Для получения дополнительной [информации см.](../log-analytics/log-analytics-get-started.md)
* Идентификатор ресурса для учетной записи Azure Automation.

Используйте следующую команду, чтобы найти идентификатор ресурса для учетной записи Azure Automation:

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Чтобы найти идентификатор ресурса для рабочего пространства Log Analytics, запустите следующую команду PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Если в выводе предыдущих команд имеется несколько учетных записей автоматизации или рабочее пространство, найдите имя, необходимое для настройки и копирования значения для идентификатора ресурса.

1. На портале Azure выберите учетную запись Автоматизации из лезвия **учетной записи Automation** и выберите **все настройки.** 
2. Из лезвия **всех настроек,** под **настройками учетной записи,** выберите **Свойства.**  
3. В лезвии **Свойств** обратите внимание на эти значения.<br> ![Автоматизация](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)свойств учетной записи.


## <a name="azure-monitor-log-records"></a>Записи журнала Azure Monitor

Диагностика Azure Automation создает два типа записей в журналах Azure Monitor, помеченных как `AzureDiagnostics`. Таблицы в следующих разделах представляют примеры записей, генерируемых Azure Automation, и типов данных, которые отображаются в результатах поиска журналов.

### <a name="job-logs"></a>Журналы заданий

| Свойство | Описание |
| --- | --- |
| TimeGenerated |Дата и время выполнения задания Runbook. |
| RunbookName_s |Имя Runbook. |
| Caller_s |Звонящее, инициироваввначало операцию. Допустимые значения: электронный адрес или system для запланированных заданий. |
| Tenant_g | GUID идентифицирует арендатора для вызываемого абонента. |
| JobId_g |GUID определяет работу runbook. |
| ResultType |Состояние задания Runbook. Возможны следующие значения:<br>Новое<br>- Создано<br>Started<br>- Остановлена<br>Приостановлено<br>Сбой<br>Завершено |
| Категория | Классификация типа данных. Для службы автоматизации значением является JobLogs. |
| OperationName | Тип операции, выполняемой в Azure. Для службы автоматизации значением является Job. |
| Ресурс | Название учетной записи Автоматизация |
| SourceSystem | Система, которую используют журналы Azure Monitor для сбора данных. Значение всегда является Azure для диагностики Azure. |
| ResultDescription |Состояние результата выполнения результатов выполнения. Возможны следующие значения:<br>— Job is started;<br>— Job Failed;<br>- Job Completed. |
| CorrelationId |Корреляция GUID работы runbook. |
| ResourceId |Идентификатор ресурса учетной записи Azure Automation в runbook. |
| SubscriptionId | ГРАФИЧЕСКИй интерфейс подписки Azure для учетной записи Автоматизации. |
| ResourceGroup | Название группы ресурсов для учетной записи Automation. |
| ResourceProvider | Поставщики ресурсов. Значение MICROSOFT. Автоматизации. |
| ResourceType | Тип ресурса. Значение AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Потоки заданий
| Свойство | Описание |
| --- | --- |
| TimeGenerated |Дата и время выполнения задания Runbook. |
| RunbookName_s |Имя Runbook. |
| Caller_s |Звонящее, инициироваввначало операцию. Допустимые значения: электронный адрес или system для запланированных заданий. |
| StreamType_s |Тип потока задания. Возможны следующие значения:<br>ход выполнения<br>Выходные данные<br>Предупреждение<br>error<br>debug<br>- Verbose. |
| Tenant_g | GUID идентифицирует арендатора для вызываемого абонента. |
| JobId_g |GUID определяет работу runbook. |
| ResultType |Состояние задания Runbook. Возможны следующие значения:<br>- In Progress |
| Категория | Классификация типа данных. Для службы автоматизации значением является JobStreams. |
| OperationName | Тип операции, выполняемой в Azure. Для службы автоматизации значением является Job. |
| Ресурс | Название учетной записи Автоматизация. |
| SourceSystem | Система, которую используют журналы Azure Monitor для сбора данных. Значение всегда является Azure для диагностики Azure. |
| ResultDescription |Описание, которое включает в себя выходной поток из runbook. |
| CorrelationId |Корреляция GUID работы runbook. |
| ResourceId |Идентификатор ресурса учетной записи Azure Automation в runbook. |
| SubscriptionId | ГРАФИЧЕСКИй интерфейс подписки Azure для учетной записи Автоматизации. |
| ResourceGroup | Название группы ресурсов для учетной записи Automation. |
| ResourceProvider | Поставщики ресурсов. Значение MICROSOFT. Автоматизации. |
| ResourceType | Тип ресурса. Значение AUTOMATIONACCOUNTS. |

## <a name="setting-up-integration-with-azure-monitor-logs"></a>Настройка интеграции с журналами Azure Monitor

1. На своем компьютере запустите Windows PowerShell на **начальном** экране.
2. Выполнить следующие команды PowerShell и отсваивать значение для значений `[your resource ID]` и `[resource ID of the log analytics workspace]` значений из предыдущего раздела.

   ```powershell-interactive
   $workspaceId = "[resource ID of the log analytics workspace]"
   $automationAccountId = "[resource ID of your Automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

После запуска этого скрипта может пройти час, прежде чем вы начнете `JobLogs` `JobStreams` видеть записи в новых или написанных журналах Azure Monitor.

Чтобы просмотреть журналы, запустите следующий запрос в поиске журналов аналитики журналов:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Проверка конфигурации

Чтобы подтвердить, что ваша учетная запись Automation отправляет журналы в рабочее пространство Log Analytics, проверьте, правильно ли настраиваются на учетную запись Автоматизация с помощью следующей команды PowerShell.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

В выходных данных убедитесь, что:

* Под, `Logs`значение `Enabled` для правда.
* `WorkspaceId`устанавливается значение `ResourceId` для рабочего пространства log Analytics.

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Просмотр журналов автоматизации в журналах Azure Monitor

Теперь, когда вы начали отправлять журналы задания Автоматизации в журналы Azure Monitor, давайте посмотрим, что вы можете сделать с этими журналами в журналах Azure Monitor.

Чтобы просмотреть журналы, выполните следующий запрос: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Отправка электронного сообщения при сбое или приостановке задания Runbook

Один из наших основных клиентов запрашивает возможность отправлять электронное сообщение или текст при возникновении ошибки в работе задания runbook.

Чтобы создать правило оповещения, начните с создания поиска журнала для записей работы Runbook, которые должны вызывать оповещение. Щелкните кнопку **Оповещение**, чтобы создать и настроить правило генерации оповещений.

1. На странице обзора рабочего пространства Log Analytics нажмите **«Вид журналов».**
2. Создайте запрос поиска журнала для оповещения, введя следующий поиск в поле запроса:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>Вы также можете группироваться по названию runbook, используя:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Если вы настроили для рабочей области журналы из более чем одной учетной записи службы автоматизации или подписки, то можете группировать оповещения по подписке или учетной записи службы автоматизации. Название учетной записи `Resource` автоматизации `JobLogs`можно найти в поле в поиске .
3. Чтобы открыть экран **Создать правило**, щелкните **+ Новое правило генерации оповещений** в верхней части страницы. Дополнительные сведения о параметрах настройки оповещения см. в статье [Оповещения журнала в Azure Monitor. Интерфейс оповещений](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Поиск всех заданий, завершенных с ошибками

Помимо оповещений о сбоях можно узнать, когда задание Runbook вызывает устранимую ошибку. В этих случаях PowerShell производит поток ошибок, но ошибки, не прекращающиеся, не приводят к приостановке или сбою в работе.

1. В рабочем пространстве журнала Analytics щелкните **журналы**.
2. В поле запроса `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`введите .
3. Нажмите кнопку **поиска.**

### <a name="view-job-streams-for-a-job"></a>Просмотр потоков заданий для задания

При отладке задания также можно захотеть заглянуть в потоки задания. Приведенный ниже запрос отображает все потоки для одного задания с GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Просмотр хронологии состояния задания

Наконец, вы можете визуализировать свою историю работы с течением времени. Для поиска для поиска состояния заданий по прошествии времени можно использовать приведенный ниже запрос.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Диаграмма хронологии состояния задания в Log Analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="removing-diagnostic-settings"></a>Удаление диагностических настроек

Чтобы удалить диагностическую настройку из учетной записи Automation, запустите следующую команду:

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Сводка

Отправив свой статус задания Automation и данные потоковой передачи в журналы Azure Monitor, вы сможете лучше понять состояние заданий автоматизации:
+ настроить оповещения, уведомляющие вас о проблемах;
+ с помощью пользовательских представлений и поисковых запросов визуализировать результаты модуля Runbook, состояние задания Runbook и другие связанные ключевые индикаторы или метрики.

Журналы Azure Monitor обеспечивают большую оперативную видимость рабочих мест автоматизации и могут быстрее устрашать инциденты.

## <a name="next-steps"></a>Дальнейшие действия

* Для устранения неполадок в журнале Analytics [см.](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data)
* Чтобы узнать больше о том, как создавать различные поисковые запросы и просматривать журналы заданий автоматизации с помощью журналов Azure Monitor, смотрите [поиск в журналах Azure Monitor.](../log-analytics/log-analytics-log-searches.md)
* Чтобы понять, как создавать и извлекать выходные и сообщения об ошибках из runbooks, [см.](automation-runbook-output-and-messages.md)
* Дополнительные сведения о выполнении модулей Runbook, отслеживании заданий модуля Runbook и других технических деталях см. в статье [Выполнение модуля Runbook в службе автоматизации Azure](automation-runbook-execution.md).
* Подробнее о журналах и источниках сбора данных Azure Monitor читайте [в обзоре журналов Azure Monitor.](../azure-monitor/platform/collect-azure-metrics-logs.md)

