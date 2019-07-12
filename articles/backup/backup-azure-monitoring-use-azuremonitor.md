---
title: 'Azure Backup: Мониторинг архивации Azure с помощью Azure Monitor'
description: Мониторинг рабочих нагрузок Azure Backup и создать настраиваемые оповещения с помощью Azure Monitor.
services: backup
author: pvrk
manager: shivamg
keywords: Log Analytics; Служба архивации Azure; Оповещения; Параметры диагностики; Группы действий
ms.service: backup
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: pullabhk
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: e2d4a235737789f2f5852c00218427613db3d558
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786317"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Мониторинг в нужном масштабе с помощью Azure Monitor

Служба архивации Azure предоставляет [встроенный мониторинг и оповещения возможности](backup-azure-monitoring-built-in-monitor.md) в хранилище служб восстановления. Эти возможности доступны без какой-либо дополнительное управление инфраструктурой. Но это встроенная учетная запись службы имеет ограничения в следующих сценариях:

- Если нужно отслеживать данные из нескольких хранилищ служб восстановления между подписками
- Если канал предпочтительный уведомлений является *не* электронной почты
- Если пользователи хотят оповещения дополнительные сценарии
- Если вы хотите просмотреть сведения из компонента в локальной, такие как System Center Data Protection Manager в Azure, что портал не отображает в [ **задания резервного копирования** ](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) или [  **Оповещения резервного копирования**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>С помощью рабочей области Log Analytics

> [!NOTE]
> Данные из резервных копий виртуальных Машин Azure, агент службы архивации Azure, System Center Data Protection Manager, резервные копии SQL на виртуальных машинах Azure и резервными копиями файловых ресурсов службой файлов Azure, поступают на рабочую область Log Analytics с помощью параметров диагностики. 

Чтобы мониторинг в нужном масштабе, вам понадобится возможности двух служб Azure. *Параметры диагностики* отправлять данные из нескольких ресурсов Azure Resource Manager на другой ресурс. *Log Analytics* создает пользовательские оповещения, где можно использовать группы действий для определения другие каналы уведомлений. 

В следующих разделах подробно, как использовать Log Analytics для мониторинга Azure Backup в нужном масштабе.

### <a name="configure-diagnostic-settings"></a>Настройка параметров диагностики

Ресурсы Azure Resource Manager, например в хранилище служб восстановления записи сведений о запланированных операций и пользователь инициировал операции как диагностических данных. 

В разделе мониторинга, выберите **параметров диагностики** и укажите целевой объект для диагностических данных в хранилище служб восстановления.

![Параметр диагностики в хранилище служб восстановления, предназначенных для Log Analytics](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

Можно назначить рабочую область Log Analytics из другой подписки. Чтобы отслеживать хранилищ между подписками в одном месте, выберите одну и ту же рабочую область Log Analytics для нескольких хранилищ служб восстановления. Чтобы канал все сведения, относящиеся к службе архивации Azure в рабочую область Log Analytics, выберите **AzureBackupReport** журналом.

> [!IMPORTANT]
> После завершения настройки нужно подождать 24 часа Принудительная начальная отправка данных для завершения. После, начальный Принудительная отправка данных, все события будут передаваться как описано далее в этой статье в [разделе частота](#diagnostic-data-update-frequency).

### <a name="deploy-a-solution-to-the-log-analytics-workspace"></a>Развертывание решения в рабочей области Log Analytics

После копирования данных внутри рабочей области Log Analytics, [развертывание шаблона GitHub](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) в Log Analytics для визуализации данных. Чтобы правильно определить рабочую область, убедитесь, что вы предоставляете группе ресурсов, имя рабочей области и расположение рабочей области. Затем можно установите этот шаблон в рабочей области.

> [!NOTE]
> Если у вас нет оповещений, задания резервного копирования или восстановления задания в рабочей области Log Analytics, вы увидите код ошибки «BadArgumentError» на портале. Не учитывать эту ошибку и продолжить использование решения. После запуска введите соответствующие данные поступают в рабочей области визуализации будут отражать же и не появится сообщение об ошибке больше.

### <a name="view-azure-backup-data-by-using-log-analytics"></a>Просмотр данных служба архивации Azure с помощью Log Analytics

После развертывания шаблона решения для мониторинга Azure Backup будут отображаться в области сводки рабочей области. Чтобы перейти к обзору, выполните одно из этих путей.

- **Azure Monitor**. В **Insights** выберите **дополнительные** и выберите соответствующую рабочую область.
- **Рабочие области log Analytics**: Выберите соответствующую рабочую область, а затем в разделе **Общие**выберите **Сводка рабочей области**.

![Плитки мониторинга Log Analytics](media/backup-azure-monitoring-laworkspace/la-azurebackup-azuremonitor-tile.png)

При выборе плитки мониторинга конструктора шаблон откроется ряд диаграмм о базовые данные мониторинга из службы архивации Azure. Вот несколько диаграмм, которые вы увидите:

* Все задания резервного копирования

   ![Log Analytics диаграмм для заданий резервного копирования](media/backup-azure-monitoring-laworkspace/la-azurebackup-allbackupjobs.png)

* Задания восстановления

   ![Граф аналитики журналов для заданий восстановления](media/backup-azure-monitoring-laworkspace/la-azurebackup-restorejobs.png)

* Встроенные оповещения Azure Backup для ресурсов Azure

   ![Граф аналитики журнала встроенные оповещения Azure Backup для ресурсов Azure](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts.png)

* Встроенные оповещения службы архивации Azure для локальных ресурсов

   ![Граф аналитики журнала встроенные оповещения службы архивации Azure для локальных ресурсов](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts-onprem.png)

* Активные источники данных

   ![Граф аналитики журнала для активных резервных копий сущностей](media/backup-azure-monitoring-laworkspace/la-azurebackup-activedatasources.png)

* Хранилище служб восстановления облачного хранилища

   ![Диаграмма журнала аналитики для облачного хранилища для хранилища служб восстановления](media/backup-azure-monitoring-laworkspace/la-azurebackup-cloudstorage-in-gb.png)

Эти графики, предоставляемые шаблоном. Можно изменить диаграммы или добавить дополнительные диаграммы, если вам нужно.

> [!IMPORTANT]
> При развертывании шаблона, вы фактически создаете блокировки только для чтения. Чтобы изменить и сохранить шаблон, необходимо снять блокировку. Можно снять блокировку в **параметры** части рабочей области Log Analytics на **блокирует** области.

### <a name="create-alerts-by-using-log-analytics"></a>Создание оповещений с помощью Log Analytics

В Azure Monitor можно создать собственные оповещения в рабочей области Log Analytics. В рабочей области используется *группах действий Azure* выберите ваш механизм предпочтительный уведомлений. 

> [!IMPORTANT]
> Сведения о ценах создания этого запроса, см. в разделе [цены на Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Выберите любой из графиков, чтобы открыть **журналы** части рабочей области Log Analytics. В **журналы** разделе, изменять запросы и создавать оповещения на них.

![Создание оповещения в рабочей области Log Analytics](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

При выборе **новое правило генерации оповещений**, открывается страница создания оповещения Azure Monitor, как показано на следующем рисунке. Здесь ресурс уже помечен как рабочей области Log Analytics, и предоставляется возможность интеграции группы действий.

![Страница создания оповещения Log Analytics](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Условие оповещения

Определяющей характеристикой оповещение является его условие триггера. Выберите **условие** автоматически загрузить запрос Kusto на **журналы** странице, как показано на следующем рисунке. Здесь вы можете изменить условия в соответствии с потребностями. Дополнительные сведения см. в разделе [запросов Kusto пример](#sample-kusto-queries).

![Настройка состояния оповещения](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

При необходимости можно изменить запрос Kusto. Выберите пороговое значение, период и частота. Пороговое значение определяет, когда будет создано предупреждение. Период — это окно времени, в котором выполняется запрос. Например если пороговое значение больше 0, период составляет 5 минут, а значение частоты — 5 минут, затем правило выполняет запрос каждые 5 минут, просмотрев предыдущие 5 минут. Если количество результатов больше 0, вы получите уведомление об через группу выбранное действие.

#### <a name="alert-action-groups"></a>Действие оповещения группы

Используйте группу действий, чтобы указать способ уведомления. Чтобы увидеть механизмы уведомление о доступности, в разделе **группы действий**выберите **Create New**.

![Уведомление о доступности механизмы в окне «Добавить группу действий»](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Позволяет удовлетворить все требования из Log Analytics только мониторинг и оповещения, или Log Analytics можно использовать для дополнения встроенные уведомления.

Дополнительные сведения см. в разделе [создание, просмотр и управление оповещений журнала с помощью Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) и [Создание групп действий на портале Azure и управление ими](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).

### <a name="sample-kusto-queries"></a>Примеры запросов Kusto

Диаграмм по умолчанию позволяют запросов Kusto для основных сценариев, на которых можно создавать оповещения. Можно также изменить все эти запросы для получения данных вы хотите получать оповещения. Вставьте в следующих примерах запросов Kusto в **журналы** странице, а затем создать оповещения для запросов:

* Все успешно выполненные задания резервного копирования

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````
    
* Все неудачные задания резервного копирования

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````
    
* Все успешно выполненных заданий для резервного копирования виртуальной Машины Azure

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

* Все успешно SQL журнала задания резервного копирования

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

* Все успешно выполненных заданий агента службы архивации Azure

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

Диагностические данные из хранилища, поступают в рабочую область Log Analytics с помощью некоторых запаздывания. Каждое событие поступает в рабочей области Log Analytics *20 – 30 минут* после он помещается в хранилище служб восстановления. Ниже приведены дополнительные сведения о задержки.

- В решениях, а все встроенные оповещения службы резервного копирования передаются как только они создаются. Поэтому они обычно отображаются в рабочей области Log Analytics через 20 – 30 минут.
- В решениях, а все задания нерегламентированного резервного копирования и восстановления будут отправляться как можно скорее они *Готово*.
- Для всех решений, за исключением резервной копии SQL, как только они передаются запланированные задания резервного копирования *Готово*.
- Для резервного копирования SQL так как резервное копирование журнала может выполняться каждые 15 минут, сведения для все завершенные запланированные задания резервного копирования, включая журналы, в пакетном режиме и помещается каждые 6 часов.
- В решениях, а все другие сведения, такие как элемент резервного копирования, политики, точки восстановления, хранилище и т. д. по крайней мере помещается *один раз в день.*
- Изменения в конфигурации резервного копирования (например, изменение политики или изменения политики) активирует принудительной передачи все связанные сведения о резервной копии.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>С помощью журналов активности в хранилище служб восстановления

> [!CAUTION]
> Приведенные ниже инструкции относятся только к *резервных копий виртуальных Машин Azure.* Эти действия нельзя использовать для решения, такие как агент службы архивации Azure, резервные копии SQL в Azure, или хранилище файлов Azure.

Можно также использовать журналы действий для получения уведомлений для событий, например успешном завершении резервного копирования. Чтобы начать работу, выполните следующие действия.

1. Войдите на портал Azure.
1. Откройте соответствующие хранилище служб восстановления. 
1. В окне свойств в хранилище откройте **журнал действий** раздел.

Определение соответствующего журнала и создать оповещение:

1. Убедитесь, что вы получили журналы действий для успешных операций резервного копирования, применяя фильтры, показанные на следующем рисунке. Изменение **Timespan** значение так, чтобы просмотреть записи.

   ![Фильтрацию, чтобы найти журналы действий для архивации виртуальных Машин Azure](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. Выберите имя операции, чтобы просмотреть соответствующие сведения.
1. Выберите **новое правило генерации оповещений** открыть **создать правило** страницы. 
1. Создать оповещение, выполнив действия, описанные в [создание, просмотр и управление оповещений журнала действий с помощью Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

   ![Новое правило генерации оповещений](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Здесь ресурс является само хранилище служб восстановления. Необходимо повторить те же действия для всех хранилищ, в которых вы хотите получать уведомления в журналах действий. Условие не будет пороговое значение, период или частоту, так как это предупреждение основана на событиях. Как только создается журнал соответствующих действий, предупреждение будет инициировано.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Мониторинг в нужном масштабе с помощью Log Analytics

Можно просмотреть все оповещения, созданные из журналов действий и рабочих областей Log Analytics в Azure Monitor. Просто откройте **оповещения** панели слева.

Несмотря на то, что вы можете получить уведомления о журналах действий, мы настоятельно рекомендуем использовать Log Analytics, а не журналы действий для мониторинга в нужном масштабе. Вот почему:

- **Ограниченные сценарии**: Уведомления о журналах действий применяются только к архивации виртуальных Машин Azure. Уведомления необходимо настроить для каждого хранилища служб восстановления.
- **Определение, в соответствии с**: Самое последнее определение журналы действий не подходит для запланированного резервного копирования действия. Вместо этого она выравнивается [журналы диагностики](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#what-you-can-do-with-diagnostic-logs). Такое выравнивание вызывает непредвиденные эффекты, если данные, которые проходят через действие регистрации канала изменений.
- **Проблемы с канал журнала действий**: В хранилищах служб восстановления журналы действий переноса из службы Azure Backup выполните новую модель. К сожалению это изменение затрагивает создание журналов действий в Azure для государственных организаций, Германии и Китая 21Vianet. Если пользователи из этих облачных служб Создайте или настройте любые предупреждения журналов действий в Azure Monitor, оповещения не создаваться. Кроме того, в всех общедоступных регионах Azure, если пользователь [собирает журналы действий служб восстановления в рабочую область Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), эти журналы не отображаются.

Использование рабочей области Log Analytics для мониторинга и оповещений в требуемом масштабе для всех рабочих нагрузок, защищенных службой Azure Backup.

## <a name="next-steps"></a>Следующие шаги

Чтобы создать пользовательские запросы, см. в разделе [модель данных Log Analytics](backup-azure-log-analytics-data-model.md).
