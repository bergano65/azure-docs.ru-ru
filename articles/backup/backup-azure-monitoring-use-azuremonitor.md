---
title: 'Azure Backup: Мониторинг архивации Azure с помощью Azure Monitor'
description: Мониторинг рабочих нагрузок Azure Backup и создать настраиваемые оповещения, с помощью Azure Monitor
services: backup
author: pvrk
manager: shivamg
keywords: Log Analytics; Служба архивации Azure; Оповещения; Параметры диагностики; Группы действий
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: pullabhk
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 94fde7714f3efe0a460983966923071bce1afcc6
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190504"
---
# <a name="monitoring-at-scale-using-azure-monitor"></a>Мониторинг в нужном масштабе с помощью Azure Monitor

[Встроенный мониторинг и оповещения статье](backup-azure-monitoring-built-in-monitor.md) перечисленные мониторинга и оповещений возможности в одном хранилище служб восстановления и который предлагается без какой-либо дополнительное управление инфраструктурой. Тем не менее встроенная служба имеет ограничения в следующих сценариях.

- Данные мониторинга из нескольких хранилищ RS между подписками
- Если адрес электронной почты не является предпочтительным уведомления
- Если пользователи хотят получать предупреждения для нескольких сценариев
- Если вы хотите просмотреть данные о локальный компонент, например System Center DPM (SC DPM) в Azure, который не содержится в [задания резервного копирования](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) или [оповещения резервного копирования](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) на портале.

## <a name="using-log-analytics-workspace"></a>С помощью рабочей области Log Analytics

> [!NOTE]
> Данные из резервных копий виртуальных Машин Azure, агент MAB, DPM System Center (SC DPM), резервные копии SQL на виртуальных машинах Azure поступают в рабочую область Log Analytics с помощью параметров диагностики. Поддержка Azure резервными копиями файловых ресурсов, сервера архивации Microsoft Azure (MABS) ожидается в ближайшее время.

Мы используя возможности двух служб Azure - **параметров диагностики** (для отправки данных из нескольких ресурсов Azure Resource Manager на другой ресурс) и **Log Analytics** (ЛА - создать пользовательские оповещения, где можно определить другие каналы уведомлений с помощью групп действий) для мониторинга в нужном масштабе. В следующих разделах подробно описывается по использованию LA для наблюдения за резервное копирование Azure в нужном масштабе.

### <a name="configuring-diagnostic-settings"></a>Настройка параметров диагностики

Это ресурс Azure Resource Manager, таких как хранилище служб восстановления Azure записывает все возможные сведения о запланированные операции и операции пользователя запускается как диагностических данных. Нажмите кнопку «Параметры диагностики» в разделе мониторинга и укажите целевой объект для диагностических данных из хранилища служб восстановления.

![Хранилища служб восстановления параметра диагностики с LA качестве целевого объекта](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

Можно выбрать рабочую область LA из другой подписки в качестве целевого объекта. *Если выбрать одну и ту же рабочую область LA для нескольких хранилищ RS, вы можете отслеживать хранилищ между подписками в одном месте.* Выберите «AzureBackupReport» в качестве журнала для канала, все сведения в рабочую область LA, связанные с Azure Backup.

> [!IMPORTANT]
> После завершения конфигурации, следует подождать 24 часа Принудительная начальная отправка данных для выполнения. После этого все события отправляются в том случае, как упоминалось в [разделе частота](#diagnostic-data-update-frequency).

### <a name="deploying-solution-to-log-analytics-workspace"></a>Развертывание решения в рабочей области Log Analytics

После того как данные в рабочую область LA, [развертывание шаблона github](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) на LA для визуализации данных. Убедитесь, что вы предоставляете группе ресурсов, имя рабочей области и расположение рабочей области правильно определить рабочую область, а затем установить этот шаблон на нем.

### <a name="view-azure-backup-data-using-log-analytics-la"></a>Просмотр данных служба архивации Azure, с помощью Log Analytics (LA)

После развертывания шаблона решения для мониторинга Azure Backup будут отображаться в области сводки рабочей области. Можно просматривать с помощью

- Azure Monitor -> «Сведения» в разделе «Insights» и выберите соответствующую рабочую область или
- Рабочие области log Analytics "->" выберите соответствующую рабочую область "->" «Сводка рабочую область» в разделе "Общие".

![AzureBackupLAMonitoringTile](media/backup-azure-monitoring-laworkspace/la-azurebackup-azuremonitor-tile.png)

При щелчке плитки, шаблон конструктора, откроется серия графов о основные данные мониторинга из службы Azure Backup, такие как

#### <a name="all-backup-jobs"></a>Все задания резервного копирования

![LABackupJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-allbackupjobs.png)

#### <a name="restore-jobs"></a>Задания восстановления

![LARestoreJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-restorejobs.png)

#### <a name="inbuilt-azure-backup-alerts-for-azure-resources"></a>Встроенный оповещений службы архивации Azure для ресурсов Azure

![LAInbuiltAzureBackupAlertsForAzureResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts.png)

#### <a name="inbuilt-azure-backup-alerts-for-on-prem-resources"></a>Встроенный оповещений службы архивации Azure для ресурсов в локальной среде

![LAInbuiltAzureBackupAlertsForOnPremResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts-onprem.png)

#### <a name="active-datasources"></a>Активные источники данных

![LAActiveBackedUpEntities](media/backup-azure-monitoring-laworkspace/la-azurebackup-activedatasources.png)

#### <a name="rs-vault-cloud-storage"></a>Хранилища служб восстановления облачного хранилища

![LARSVaultCloudStorage](media/backup-azure-monitoring-laworkspace/la-azurebackup-cloudstorage-in-gb.png)

Выше графами с помощью шаблона и клиент может изменить или добавить дополнительные диаграммы.

> [!IMPORTANT]
> При развертывании шаблона, по сути, создает блокировки только для чтения, и вам нужно удалить его, чтобы изменить шаблон и сохранить. Чтобы удалить блокировки, найдите в области «Блокировки» рабочей области Log Analytics в разделе «Параметры».

### <a name="create-alerts-using-log-analytics"></a>Создание оповещения с помощью Log Analytics

Azure Monitor позволяет пользователям создавать свои собственные оповещения из LA рабочей области, где вы можете *используют группы действий Azure для выбора Ваш предпочитаемый уведомлений механизм*. Щелкните любой из диаграмм, выше, чтобы открыть рабочую область LA в разделе «Журналы» ***где можно изменять запросы и создавать оповещения на основе их***.

![LACreateAlerts](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

Щелкнуть «Новое правило генерации оповещений» как показано выше, откроется экран создания оповещения Azure Monitor.

Как можно заметить ниже, ресурс уже помечен как рабочую область LA и предоставляется возможность интеграции группы действий.

![LAAzureBackupCreateAlert](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

> [!IMPORTANT]
> Обратите внимание, что соответствующие расходами создания этого запроса предоставляется [здесь](https://azure.microsoft.com/pricing/details/monitor/).

#### <a name="alert-condition"></a>Условие оповещения

Ключевым аспектом является условие триггера оповещения. Щелкнув «Условие» будет автоматически загружаться запрос Kusto на экране «Журналы», как показано ниже, и вы его можно изменить в соответствии с вашим сценарием. Некоторые примеры запросов Kusto приведены в [раздел ниже](#sample-kusto-queries).

![LAAzureBackupAlertCondition](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Измените запрос Kusto, при необходимости, щелкните правой пороговое значение (которое будет принято решение срабатывания оповещения), точка справа (для которого выполняется запрос временного окна) и частоты. Например:  Если пороговое значение больше 0, период составляет 5 минут, а значение частоты — 5 минут, то правило будет преобразован как «Выполнить запрос каждые 5 минут, за последние 5 минут и если количество результатов больше 0, уведомлять меня по группе выбранное действие»

#### <a name="action-group-integration"></a>Интеграция группы действий

Группы действий позволяют указать каналов уведомлений, которые доступны пользователю. Щелкнув «Создать» в разделе «Группы действий» отображается список доступных механизмов уведомлений.

![LAAzureBackupNewActionGroup](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Дополнительные сведения о [оповещений из рабочей области LA](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) и около [группы действий](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) из документации по Azure Monitor.

Таким образом можно удовлетворить все требования из LA только мониторинг и оповещения или использовать его в качестве дополнительных методика уведомлений встроенные механизмы.

### <a name="sample-kusto-queries"></a>Примеры запросов Kusto

Диаграмм по умолчанию даст запросов Kusto для основных сценариев, на которых можно создавать оповещения. Можно также изменить их для получения данных, которую вы хотите получать предупреждения об. Здесь представлены некоторые примеры запросов Kusto, которые можно вставить в окне «Журналы», а затем создать оповещение на запрос.

#### <a name="all-successful-backup-jobs"></a>Все успешно выполненные задания резервного копирования

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Completed"
````

#### <a name="all-failed-backup-jobs"></a>Все неудачные задания резервного копирования

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Failed"
````

#### <a name="all-successful-azure-vm-backup-jobs"></a>Все успешно выполненных заданий для резервного копирования виртуальной Машины Azure

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
| where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
| join kind=inner
(
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where OperationName == "BackupItem"
    | where SchemaVersion_s == "V2"
    | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
    | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
    | project BackupItemUniqueId_s , BackupItemFriendlyName_s
)
on BackupItemUniqueId_s
| extend Vault= Resource
| project-away Resource
````

#### <a name="all-successful-sql-log-backup-jobs"></a>Все успешно выполненных заданий для резервного копирования журнала SQL

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
| where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
| join kind=inner
(
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where OperationName == "BackupItem"
    | where SchemaVersion_s == "V2"
    | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
    | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
    | project BackupItemUniqueId_s , BackupItemFriendlyName_s
)
on BackupItemUniqueId_s
| extend Vault= Resource
| project-away Resource
````

#### <a name="all-successful-mab-agent-backup-jobs"></a>Все успешно выполненных заданий агента MAB резервного копирования

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
| where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
| join kind=inner
(
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where OperationName == "BackupItem"
    | where SchemaVersion_s == "V2"
    | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
    | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
    | project BackupItemUniqueId_s , BackupItemFriendlyName_s
)
on BackupItemUniqueId_s
| extend Vault= Resource
| project-away Resource
````

### <a name="diagnostic-data-update-frequency"></a>Частота обновлений диагностических данных

Диагностические данные из хранилища, поступают в рабочую область LA с некоторых запаздывания. Каждое событие поступает в рабочую область LA ***с отставанием в 20 – 30 минут после его передаче из хранилища служб восстановления.***

- Встроенные оповещения службы резервного копирования (для всех решений) отправляются сразу же после их создания. Это означает, что они обычно отображаются в рабочую область LA после отставанием в 20 – 30 минут.
- Adhoc заданий резервного копирования и восстановления (для всех решений) передаются как можно скорее они ***завершения***.
- Запланированные задания резервного копирования из всех решений (за исключением SQL backup) передаются как можно скорее они ***завершения***.
- Для резервного копирования SQL поскольку мы имеем резервные копии журнала каждые 15 минут для все завершенные запланированные задания резервного копирования, включая журналы, данные в пакетном режиме и отправлена каждые 6 часов.
- Все другие сведения, такие как элемент резервного копирования, политику, точки восстановления, хранения и т.д. через все решения будут передаваться **по меньшей мере один раз в день.**
- Изменения в конфигурации резервного копирования, таких как изменение политики и т.д. для изменения политики активирует принудительной передачи все связанные сведения о резервной копии.

## <a name="using-rs-vaults-activity-logs"></a>С помощью хранилища служб восстановления журналов действий

Можно также использовать журналы действий для получения уведомлений для событий, например успешном завершении резервного копирования.

> [!CAUTION]
> **Учтите, что это значение применимо только для архивации виртуальных Машин Azure.** Этот мастер нельзя использовать для других решений например агента службы архивации Azure, резервные копии SQL в Azure, файлов Azure и пр.

### <a name="sign-in-into-azure-portal"></a>Войдите портал Azure

Войдите на портал Azure и перейдите к соответствующей хранилище служб восстановления Azure и нажмите кнопку в разделе «Журнал действий» в свойствах.

### <a name="identify-appropriate-log-and-create-alert"></a>Определение соответствующего журнала и создание оповещения

Примените фильтры, показанные на следующем рисунке, чтобы проверить, получаете ли вы журналы действий для успешных операций резервного копирования. Соответствующим образом измените период времени для просмотра записей.

![Журналы действий для архивации виртуальных Машин Azure](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

Можно щелкнуть сегмент "JSON", чтобы получить более подробные сведения и просмотреть их, скопировав и вставив в текстовый редактор. Он должен отображать информацию о хранилище и элемента, который инициировал действие входа т. е. элементе резервного копирования.

Затем щелкните "Добавить оповещение журнала действий", чтобы создавать оповещения для всех подобных журналов.

Можно щелкнуть «Добавить оповещение журнала действий» показано выше, и он будет открыт экран создания предупреждения, который похож на экран создания предупреждения [описанным выше](#create-alerts-using-log-analytics).

Вот хранилища служб восстановления, сам ресурс, и таким образом необходимо повторить те же действия для всех хранилищ, в которых требуется уведомление о журналах действий. Условие не будет иметь любое пороговое значение, точка, частота, так как это оповещение на основе событий. Как только создается журнал соответствующих действий, предупреждения.

## <a name="recommendation"></a>Рекомендации

***Все оповещения, созданные из журналов действий и рабочих областей LA можно просмотреть в Azure Monitor в области «Оповещения» слева.***

Хотя можно использовать уведомление с помощью журналов действий, ***службы архивации Azure настоятельно рекомендует использовать для отслеживания на масштаб и не журналов действий по следующим причинам LA***.

- **Ограниченном наборе сценариев:** Применимо только для архивации виртуальных Машин Azure и должна повторяться для каждого хранилища служб восстановления.
- **Определение, в соответствии с:** Запланированное действие резервного копирования не соответствуют самое последнее определение журналы действий и выравнивается [журналы диагностики](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#what-are-azure-monitor-diagnostic-logs). Это приводило к непредвиденных последствий при изменении данных, заполнение через канал журнала действий, как указано ниже.
- **Проблемы с канал журнала действий:** Мы перешли на новую модель отправки журналов действий из службы архивации Azure в хранилища служб восстановления. К сожалению перемещение повлияла создания журналов действий в независимых облаках Azure. Если пользователи Sovereign облако Azure создан или не настроен все оповещения из журналов действий с помощью Azure Monitor, они бы не активироваться. Кроме того, в всех общедоступных регионах Azure, когда пользователь собирает журналы действий служб восстановления в рабочую область аналитики журналов, как уже упоминалось [здесь](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), эти журналы также не будут отображаться.

Поэтому настоятельно рекомендуется для использования журнала аналитики рабочей области для мониторинга и создания оповещений с возможностью масштабирования для Azure Backup для защищаемых рабочих нагрузок.

## <a name="next-steps"></a>Дальнейшие действия

- Ссылаться на [модель данных Log analytics](backup-azure-log-analytics-data-model.md) для создания пользовательских запросов.
