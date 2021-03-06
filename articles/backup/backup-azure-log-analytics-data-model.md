---
title: Модель данных журналов Azure Monitor
description: В этой статье содержатся сведения о Azure Monitor Log Analytics сведения о модели данных для Azure Backup данных.
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: eed3f66c1743bb21118a2d90343989d2b6a081de
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278491"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Модель данных Log Analytics для данных Azure Backup

Используйте Log Analyticsную модель данных для создания настраиваемых оповещений из Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
>
> Эта модель данных является ссылкой на система диагностики Azure режим отправки диагностических событий в Log Analytics (LA). Чтобы узнать модель данных для нового режима, относящегося к ресурсу, см. следующую статью: [модель данных для Azure Backup событий диагностики](https://aka.ms/diagnosticsdatamodel) .

## <a name="using-azure-backup-data-model"></a>Использование модели данных Azure Backup

Вы можете использовать следующие поля, предоставляемые как часть модели данных, для создания визуальных элементов, пользовательских запросов и панели мониторинга в соответствии с вашими требованиями.

### <a name="alert"></a>Предупреждение

Эта таблица содержит сведения о полях, связанных с предупреждением.

| Поле | Тип данных | ОПИСАНИЕ |
| --- | --- | --- |
| AlertUniqueId_s |текст |Уникальный идентификатор созданного предупреждения |
| AlertType_s |текст |Тип предупреждения, например "Резервная копия" |
| AlertStatus_s |текст |Состояние предупреждения, например активное |
| AlertOccurrenceDateTime_s |Дата и время |Дата и время создания предупреждения |
| AlertSeverity_s |текст |Серьезность предупреждения, например критическая |
|AlertTimeToResolveInMinutes_s    | Число        |Время, затраченное на разрешение предупреждения. Пусто для активных оповещений.         |
|AlertConsolidationStatus_s   |текст         |Определяет, является ли предупреждение консолидированным предупреждением         |
|CountOfAlertsConsolidated_s     |Число         |Число консолидированных предупреждений, если это консолидированное предупреждение          |
|AlertRaisedOn_s     |текст         |Тип сущности, на которой создается оповещение         |
|AlertCode_s     |текст         |Код для уникальной идентификации типа оповещения         |
|RecommendedAction_s   |текст         |Рекомендованное действие для разрешения предупреждения         |
| EventName_s |текст |Имя события. Всегда AzureBackupCentralReport |
| BackupItemUniqueId_s |текст |Уникальный идентификатор архивируемого элемента, связанного с предупреждением |
| SchemaVersion_s |текст |Текущая версия схемы, например **v2** |
| State_s |текст |Текущее состояние объекта предупреждения, например "Активный" или "Удален" |
| BackupManagementType_s |текст |Тип поставщика для выполнения архивации, например IaaSVM, FileFolder, к которому относится это предупреждение |
| OperationName |текст |Имя текущей операции, например "Предупреждение" |
| Категория |текст |Категория диагностических данных, отправленных в журналы Azure Monitor. Всегда AzureBackupReport |
| Resource (Ресурс) |текст |Ресурс, для которого собираются данные. Отображается имя хранилища служб восстановления |
| ProtectedContainerUniqueId_s |текст |Уникальный идентификатор защищенного сервера, связанного с предупреждением (был ProtectedServerUniqueId_s в v1)|
| VaultUniqueId_s |текст |Уникальный идентификатор защищенного хранилища, связанного с предупреждением |
| SourceSystem |текст |Исходная система текущих данных (Azure) |
| ResourceId |текст |Уникальный идентификатор для ресурса, по которому собираются данные. Например, идентификатор ресурса хранилища служб восстановления |
| SubscriptionId |текст |Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |текст |Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |текст |Поставщик ресурсов, для которого собираются данные. Например, Microsoft.RecoveryServices |
| ResourceType |текст |Тип ресурса, для которого собираются данные. Например, "Хранилище" |

### <a name="backupitem"></a>BackupItem

Эта таблица содержит сведения о полях, связанных с архивируемым элементом.

| Поле | Тип данных | ОПИСАНИЕ |
| --- | --- | --- |
| EventName_s |текст |Имя события. Всегда AzureBackupCentralReport |  
| BackupItemUniqueId_s |текст |Уникальный идентификатор архивируемого элемента |
| BackupItemId_s |текст |Идентификатор элемента резервного копирования (это поле предназначено только для схемы v1) |
| BackupItemName_s |текст |Имя архивируемого элемента |
| BackupItemFriendlyName_s |текст |Понятное имя архивируемого элемента |
| BackupItemType_s |текст |Тип архивируемого элемента, например виртуальная машина, папка с файлами |
| BackupItemProtectionState_s |текст |Состояние защиты элемента архивации |
| BackupItemAppVersion_s |текст |Версия приложения элемента резервного копирования |
| ProtectionState_s |текст |Текущее состояние защиты архивируемого элемента, например "Защищено" или "Защита остановлена" |
| ProtectionGroupName_s |текст | Имя группы защиты, в которой защищен элемент резервного копирования, для SC DPM и MABS, если применимо|
| SecondaryBackupProtectionState_s |текст |Включена ли вторичная Защита для элемента резервного копирования|
| SchemaVersion_s |текст |Версия схемы, например **v2** |
| State_s |текст |Состояние объекта архивируемого элемента, например "Активный" или "Удален" |
| BackupManagementType_s |текст |Тип поставщика для выполнения архивации, например IaaSVM, FileFolder, к которому принадлежит этот архивируемый элемент |
| OperationName |текст |Имя операции, например BackupItem |
| Категория |текст |Категория диагностических данных, отправленных в журналы Azure Monitor. Всегда AzureBackupReport |
| Resource (Ресурс) |текст |Ресурс, для которого собираются данные, например имя хранилища служб восстановления |
| SourceSystem |текст |Исходная система текущих данных (Azure) |
| ResourceId |текст |Идентификатор ресурса для собираемых данных, например идентификатор ресурса хранилища служб восстановления |
| SubscriptionId |текст |Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |текст |Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |текст |Поставщик ресурсов для собираемых данных, например Microsoft.RecoveryServices |
| ResourceType |текст |Тип ресурса для собираемых данных, например "Хранилище" |

### <a name="backupitemassociation"></a>BackupItemAssociation

Эта таблица содержит сведения об ассоциациях архивируемого элемента с различными сущностями.

| Поле | Тип данных | ОПИСАНИЕ |
| --- | --- | --- |
| EventName_s |текст |Имя события. Это всегда AzureBackupCentralReport |  
| BackupItemUniqueId_s |текст |Уникальный идентификатор элемента архивации |
| SchemaVersion_s |текст |Это поле обозначает текущую версию схемы, то есть **v2** |
| State_s |текст |Текущее состояние ассоциации архивируемого элемента, например "Активный" или "Удален" |
| BackupManagementType_s |текст |Тип поставщика для сервера, выполняющего задание резервного копирования, например IaaSVM, FileFolder |
| BackupItemSourceSize_s |текст | Внешний размер элемента резервного копирования |
| BackupManagementServerUniqueId_s |текст | Поле для уникальной идентификации сервера управления архивацией, с которым защищен элемент резервного копирования, если применимо |
| Категория |текст |Категория данных диагностики, отправляемых в Log Analytics (AzureBackupReport) |
| OperationName |текст |Имя текущей операции (BackupItemAssociation) |
| Resource (Ресурс) |текст |Ресурс, для которого собираются данные. Отображается имя хранилища служб восстановления |
| ProtectedContainerUniqueId_s |текст |Уникальный идентификатор защищенного сервера, связанного с элементом резервного копирования (был ProtectedServerUniqueId_s в v1) |
| VaultUniqueId_s |текст |Уникальный идентификатор хранилища, которое содержит архивируемый элемент |
| SourceSystem |текст |Исходная система текущих данных (Azure) |
| ResourceId |текст |Идентификатор ресурса для собираемых данных. Например, идентификатор ресурса хранилища служб восстановления |
| SubscriptionId |текст |Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |текст |Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |текст |Поставщик ресурсов для собираемых данных, например Microsoft.RecoveryServices |
| ResourceType |текст |Тип ресурса для данных, которые собираются, например "Хранилище" |

### <a name="backupmanagementserver"></a>баккупманажементсервер

Эта таблица содержит сведения об ассоциациях архивируемого элемента с различными сущностями.

| Поле | Тип данных | ОПИСАНИЕ |
| --- | --- | --- |
|BackupManagementServerName_s     |текст         |Имя сервера управления архивацией        |
|AzureBackupAgentVersion_s     |текст         |Версия агента Azure Backup на сервере управления архивацией          |
|BackupManagementServerVersion_s     |текст         |Версия сервера управления архивацией|
|BackupManagementServerOSVersion_s    |текст            |Версия ОС сервера управления архивацией|
|BackupManagementServerType_s     |текст         |Тип сервера управления архивацией, например MABS, SC DPM|
|BackupManagementServerUniqueId_s     |текст         |Поле для уникальной идентификации сервера управления архивацией       |
| SourceSystem |текст |Исходная система текущих данных (Azure) |
| ResourceId |текст |Идентификатор ресурса для собираемых данных. Например, идентификатор ресурса хранилища служб восстановления |
| SubscriptionId |текст |Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |текст |Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |текст |Поставщик ресурсов для собираемых данных, например Microsoft.RecoveryServices |
| ResourceType |текст |Тип ресурса для данных, которые собираются, например "Хранилище" |

### <a name="job"></a>Job

Эта таблица содержит сведения о полях, связанных с заданием.

| Поле | Тип данных | ОПИСАНИЕ |
| --- | --- | --- |
| EventName_s |текст |Имя события. Всегда AzureBackupCentralReport |
| BackupItemUniqueId_s |текст |Уникальный идентификатор архивируемого элемента |
| SchemaVersion_s |текст |Версия схемы, например **v2** |
| State_s |текст |Текущее состояние объекта задания, например "Активный" или "Удален" |
| BackupManagementType_s |текст |Тип поставщика для сервера, выполняющего задание резервного копирования, например IaaSVM, FileFolder |
| OperationName |текст |Имя текущей операции (Job) |
| Категория |текст |Это поле представляет категорию диагностических данных, отправленных в журналы Azure Monitor. это установите azurebackupreport |
| Resource (Ресурс) |текст |Ресурс, для которого собираются данные. Отображается имя хранилища служб восстановления |
| ProtectedServerUniqueId_s |текст |Уникальный идентификатор защищенного сервера, связанного с заданием |
| ProtectedContainerUniqueId_s |текст | Уникальный идентификатор для идентификации защищенного контейнера, на котором выполняется задание |
| VaultUniqueId_s |текст |Уникальный идентификатор защищенного хранилища |
| JobOperation_s |текст |Операция, для которой запущено задание, например архивация, восстановление, настройка архивации |
| JobStatus_s |текст |Состояние завершенного задания, например "Завершено" или "Ошибка" |
| JobFailureCode_s |текст |Строка кода ошибки, из-за которой произошел сбой задания |
| JobStartDateTime_s |Дата и время |Дата и время начала выполнения задания |
| BackupStorageDestination_s |текст |Место назначения хранилища резервных копий, например облако или диск  |
| AdHocOrScheduledJob_s |текст | Поле, определяющее, является ли задание нерегламентированным или запланированным |
| JobDurationInSecs_s | Число |Общая длительность задания в секундах |
| DataTransferredInMB_s | Число |Переданные данные (в МБ) для этого задания|
| JobUniqueId_g |текст |Уникальный идентификатор задания |
| RecoveryJobDestination_s |текст | Назначение задания восстановления, в которое восстанавливаются данные |
| RecoveryJobRPDateTime_s |DateTime | Дата и время создания восстанавливаемой точки восстановления |
| RecoveryJobRPLocation_s |текст | Место хранения восстанавливаемой точки восстановления|
| SourceSystem |текст |Исходная система текущих данных (Azure) |
| ResourceId |текст |Идентификатор ресурса для собираемых данных. Например, идентификатор ресурса хранилища служб восстановления|
| SubscriptionId |текст |Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |текст |Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |текст |Поставщик ресурсов, для которого собираются данные. Например, Microsoft.RecoveryServices |
| ResourceType |текст |Тип ресурса, для которого собираются данные. Например, "Хранилище" |

### <a name="policy"></a>Политика

Эта таблица содержит сведения о полях, связанных с политикой.

| Поле | Тип данных | Применимые версии | ОПИСАНИЕ |
| --- | --- | --- | --- |
| EventName_s |текст ||Имя события. Это всегда AzureBackupCentralReport |
| SchemaVersion_s |текст ||Это поле обозначает текущую версию схемы, то есть **v2** |
| State_s |текст ||Текущее состояние объекта политики, например "Активный" или "Удален" |
| BackupManagementType_s |текст ||Тип поставщика для сервера, выполняющего задание резервного копирования, например IaaSVM, FileFolder |
| OperationName |текст ||Имя текущей операции (Policy) |
| Категория |текст ||Это поле представляет категорию диагностических данных, отправленных в журналы Azure Monitor. это установите azurebackupreport |
| Resource (Ресурс) |текст ||Ресурс, для которого собираются данные. Отображается имя хранилища служб восстановления |
| PolicyUniqueId_g |текст ||Уникальный идентификатор для идентификации политики |
| PolicyName_s |текст ||Указанное имя политики |
| BackupFrequency_s |текст ||Частота выполнения архивации, например ежедневно или еженедельно |
| BackupTimes_s |текст ||Дата и время запланированной архивации |
| BackupDaysOfTheWeek_s |текст ||Дни недели, на которые запланированы задания архивации |
| RetentionDuration_s |Целое число ||Длительность хранения настроенных резервных копий |
| DailyRetentionDuration_s |Целое число ||Общая длительность хранения (в днях) для настроенных резервных копий |
| DailyRetentionTimes_s |текст ||Дата и время настройки ежедневного сохранения |
| WeeklyRetentionDuration_s |Десятичное число ||Общая длительность еженедельного хранения в неделях для настроенных резервных копий |
| WeeklyRetentionTimes_s |текст ||Дата и время настройки еженедельного хранения |
| WeeklyRetentionDaysOfTheWeek_s |текст ||Дни недели, выбранные для еженедельного хранения |
| MonthlyRetentionDuration_s |Десятичное число ||Общая длительность хранения (в месяцах) для настроенных резервных копий |
| MonthlyRetentionTimes_s |текст ||Дата и время настройки ежемесячного хранения |
| MonthlyRetentionFormat_s |текст ||Тип конфигурации для ежемесячного хранения, например ежедневно для хранения по дням, еженедельно для хранения по неделям |
| MonthlyRetentionDaysOfTheWeek_s |текст ||Дни недели, выбранные для ежемесячного хранения |
| MonthlyRetentionWeeksOfTheMonth_s |текст ||Недели месяца, настроенные для ежемесячного хранения, например первая, последняя и т. д. |
| YearlyRetentionDuration_s |Десятичное число ||Общая длительность хранения (в годах) для настроенных резервных копий |
| YearlyRetentionTimes_s |текст ||Дата и время настройки ежегодного хранения |
| YearlyRetentionMonthsOfTheYear_s |текст ||Месяцы года, выбранные для ежегодного хранения |
| YearlyRetentionFormat_s |текст ||Тип конфигурации для ежегодного хранения, например ежедневно для хранения по дням, еженедельно для хранения по неделям | |
| YearlyRetentionDaysOfTheMonth_s |текст ||Дни месяца, выбранные для ежегодного хранения |
| SynchronisationFrequencyPerDay_s |Целое число |версия 2|Число раз в день синхронизации резервной копии файлов для SC DPM и MABS |
| DiffBackupFormat_s |текст |версия 2|Формат разностных резервных копий для SQL в службе архивации виртуальных машин Azure |
| DiffBackupTime_s |Время |версия 2|Время для разностных резервных копий SQL в службе архивации виртуальных машин Azure|
| DiffBackupRetentionDuration_s |Десятичное число |версия 2|Продолжительность хранения разностных резервных копий для SQL в службе архивации виртуальных машин Azure|
| LogBackupFrequency_s |Десятичное число |версия 2|Периодичность резервного копирования журналов для SQL|
| LogBackupRetentionDuration_s |Десятичное число |версия 2|Продолжительность хранения резервных копий журналов для SQL в службе архивации виртуальных машин Azure|
| DiffBackupDaysofTheWeek_s |текст |версия 2|Дни недели для разностных резервных копий SQL в службе архивации виртуальных машин Azure|
| SourceSystem |текст ||Исходная система текущих данных (Azure) |
| ResourceId |текст ||Идентификатор ресурса для собираемых данных. Например, идентификатор ресурса хранилища служб восстановления |
| SubscriptionId |текст ||Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |текст ||Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |текст ||Поставщик ресурсов, для которого собираются данные. Например, Microsoft.RecoveryServices |
| ResourceType |текст ||Тип ресурса, для которого собираются данные. Например, "Хранилище" |

### <a name="policyassociation"></a>PolicyAssociation

Эта таблица содержит сведения об ассоциациях политики с различными сущностями.

| Поле | Тип данных | Применимые версии | ОПИСАНИЕ |
| --- | --- | --- | --- |
| EventName_s |текст ||Имя события. Это всегда AzureBackupCentralReport |
| SchemaVersion_s |текст ||Это поле обозначает текущую версию схемы, то есть **v2** |
| State_s |текст ||Текущее состояние объекта политики, например "Активный" или "Удален" |
| BackupManagementType_s |текст ||Тип поставщика для сервера, выполняющего задание резервного копирования, например IaaSVM, FileFolder |
| OperationName |текст ||Имя текущей операции (PolicyAssociation) |
| Категория |текст ||Это поле представляет категорию диагностических данных, отправленных в журналы Azure Monitor. это установите azurebackupreport |
| Resource (Ресурс) |текст ||Ресурс, для которого собираются данные. Отображается имя хранилища служб восстановления |
| PolicyUniqueId_g |текст ||Уникальный идентификатор для идентификации политики |
| VaultUniqueId_s |текст ||Уникальный идентификатор хранилища, к которому принадлежит эта политика |
| BackupManagementServerUniqueId_s |текст |версия 2 |Поле для уникальной идентификации сервера управления архивацией, с которым защищен элемент резервного копирования, если применимо        |
| SourceSystem |текст ||Исходная система текущих данных (Azure) |
| ResourceId |текст ||Идентификатор ресурса для собираемых данных. Например, идентификатор ресурса хранилища служб восстановления |
| SubscriptionId |текст ||Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |текст ||Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |текст ||Поставщик ресурсов, для которого собираются данные. Например, Microsoft.RecoveryServices |
| ResourceType |текст ||Тип ресурса, для которого собираются данные. Например, "Хранилище" |

### <a name="protected-container"></a>Защищенный контейнер

В этой таблице приводятся основные поля о защищенных контейнерах. (Было ProtectedServerо в v1)

| Поле | Тип данных | ОПИСАНИЕ |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |текст | Поле для уникальной идентификации защищенного контейнера |
| ProtectedContainerOSType_s |текст |Тип ОС защищенного контейнера |
| ProtectedContainerOSVersion_s |текст |Версия ОС защищенного контейнера |
| AgentVersion_s |текст |Номер версии агента резервного копирования или агента защиты (в случае с SC DPM и MABS) |
| BackupManagementType_s |текст |Тип поставщика для выполнения резервного копирования. Например, IaaSVM, FileFolder |
| EntityState_s |текст |Текущее состояние объекта защищенного сервера. Например, "активный", "удален" |
| ProtectedContainerFriendlyName_s |текст |Понятное имя защищенного сервера |
| ProtectedContainerName_s |текст |Имя защищенного контейнера |
| ProtectedContainerWorkloadType_s |текст |Тип защищенного контейнера, резервная копия которого была создана. Например, Иаасвмконтаинер |
| ProtectedContainerLocation_s |текст |Находится ли защищенный контейнер в локальной среде или в Azure |
| ProtectedContainerType_s |текст |Является ли защищенный контейнер сервером или контейнером |
| ProtectedContainerProtectionState_s "  |текст |Состояние защиты защищенного контейнера |

### <a name="storage"></a>Служба хранилища

Эта таблица содержит сведения о полях, связанных с хранилищем.

| Поле | Тип данных | ОПИСАНИЕ |
| --- | --- | --- |
| CloudStorageInBytes_s |Десятичное число |Хранилище облачных резервных копий, используемое резервными копиями, рассчитанное на основе последнего значения (это поле предназначено только для схемы v1)|
| ProtectedInstances_s |Десятичное число |Число защищенных экземпляров, используемое для вычисления стоимости использования внешнего хранилища для выставления счетов на основе последнего значения |
| EventName_s |текст |Имя события. Это всегда AzureBackupCentralReport |
| SchemaVersion_s |текст |Это поле обозначает текущую версию схемы, то есть **v2** |
| State_s |текст |Текущее состояние объекта хранилища, например "Активный" или "Удален" |
| BackupManagementType_s |текст |Тип поставщика для сервера, выполняющего задание резервного копирования, например IaaSVM, FileFolder |
| OperationName |текст |Имя текущей операции (Storage) |
| Категория |текст |Это поле представляет категорию диагностических данных, отправленных в журналы Azure Monitor. это установите azurebackupreport |
| Resource (Ресурс) |текст |Ресурс, для которого собираются данные. Отображается имя хранилища служб восстановления |
| ProtectedServerUniqueId_s |текст |Уникальный идентификатор защищенного сервера, для которого вычисляется хранилище |
| VaultUniqueId_s |текст |Будет рассчитан уникальный идентификатор хранилища для хранилища |
| SourceSystem |текст |Исходная система текущих данных (Azure) |
| ResourceId |текст |Идентификатор ресурса для собираемых данных. Например, идентификатор ресурса хранилища служб восстановления |
| SubscriptionId |текст |Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |текст |Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |текст |Поставщик ресурсов, для которого собираются данные. Например, Microsoft.RecoveryServices |
| ResourceType |текст |Тип ресурса, для которого собираются данные. Например, "Хранилище" |
| StorageUniqueId_s |текст |Уникальный идентификатор, используемый для идентификации сущности хранилища |
| StorageType_s |текст |Тип хранилища, например облако, том, диск |
| StorageName_s |текст |Имя сущности хранилища, например E:\ |
| StorageTotalSizeInGBs_s |текст |Общий размер хранилища в ГБ, потребляемый сущностью хранилища|

### <a name="storageassociation"></a>сторажеассоЦиатион

В этой таблице представлены основные связанные с хранилищем поля, связывающие хранилище с другими сущностями.

| Поле | Тип данных | ОПИСАНИЕ |
| --- | --- |  --- |
| StorageUniqueId_s |текст |Уникальный идентификатор, используемый для идентификации сущности хранилища |
| SchemaVersion_s |текст |Это поле обозначает текущую версию схемы, то есть **v2** |
| BackupItemUniqueId_s |текст |Уникальный идентификатор, используемый для идентификации элемента резервного копирования, связанного с сущностью хранилища |
| BackupManagementServerUniqueId_s |текст |Уникальный идентификатор, используемый для идентификации сервера управления архивацией, связанного с сущностью хранилища|
| VaultUniqueId_s |текст |Уникальный идентификатор, используемый для идентификации хранилища, связанного с сущностью хранилища|
| StorageConsumedInMBs_s |Число|Размер хранилища, используемого соответствующим элементом резервного копирования в соответствующем хранилище |
| StorageAllocatedInMBs_s |Число |Размер хранилища, выделенного соответствующим элементом резервного копирования в соответствующем хранилище типа "диск"|

### <a name="vault"></a>Хранилище

Эта таблица содержит сведения о полях, связанных с хранилищем.

| Поле | Тип данных | ОПИСАНИЕ |
| --- | --- | --- |
| EventName_s |текст |Имя события. Это всегда AzureBackupCentralReport |
| SchemaVersion_s |текст |Это поле обозначает текущую версию схемы, то есть **v2** |
| State_s |текст |Текущее состояние объекта хранилища, например "Активный" или "Удален" |
| OperationName |текст |Имя текущей операции (Vault) |
| Категория |текст |Это поле представляет категорию диагностических данных, отправленных в журналы Azure Monitor. это установите azurebackupreport |
| Resource (Ресурс) |текст |Ресурс, для которого собираются данные. Отображается имя хранилища служб восстановления |
| VaultUniqueId_s |текст |Уникальный идентификатор хранилища |
| VaultName_s |текст |Имя хранилища |
| AzureDataCenter_s |текст |Центр обработки данных, где находится хранилище |
| StorageReplicationType_s |текст |Тип репликации хранилища, например геоизбыточная |
| SourceSystem |текст |Исходная система текущих данных (Azure) |
| ResourceId |текст |Идентификатор ресурса для собираемых данных. Например, идентификатор ресурса хранилища служб восстановления |
| SubscriptionId |текст |Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |текст |Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |текст |Поставщик ресурсов, для которого собираются данные. Например, Microsoft.RecoveryServices |
| ResourceType |текст |Тип ресурса, для которого собираются данные. Например, "Хранилище" |

### <a name="backup-management-server"></a>Сервер управления архивацией

В этой таблице приводятся основные поля, посвященные серверам управления архивацией.

|Поле  |Тип данных  | ОПИСАНИЕ  |
|---------|---------|----------|
|BackupManagementServerName_s     |текст         |Имя сервера управления архивацией        |
|AzureBackupAgentVersion_s     |текст         |Версия агента Azure Backup на сервере управления архивацией          |
|BackupManagementServerVersion_s     |текст         |Версия сервера управления архивацией|
|BackupManagementServerOSVersion_s     |текст            |Версия ОС сервера управления архивацией|
|BackupManagementServerType_s     |текст         |Тип сервера управления архивацией, например MABS, SC DPM|
|BackupManagementServerUniqueId_s     |текст         |Поле для уникальной идентификации сервера управления архивацией       |

### <a name="preferredworkloadonvolume"></a>преферредворклоадонволуме

В этой таблице указаны рабочие нагрузки, с которыми связан том.

| Поле | Тип данных | ОПИСАНИЕ |
| --- | --- | --- |
| StorageUniqueId_s |текст |Уникальный идентификатор, используемый для идентификации сущности хранилища |
| BackupItemType_s |текст |Рабочие нагрузки, для которых этот том является предпочтительным хранилищем|

### <a name="protectedinstance"></a>протектединстанце

В этой таблице представлены базовые связанные с защищенными экземплярами поля.

| Поле | Тип данных |Применимые версии | ОПИСАНИЕ |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |текст |версия 2|Уникальный идентификатор, используемый для идентификации элемента резервного копирования для виртуальных машин, резервное копирование которых осуществляется с помощью DPM, MABS|
| ProtectedContainerUniqueId_s |текст |версия 2|Уникальный идентификатор, используемый для идентификации защищенного контейнера для всех, кроме виртуальных машин, резервное копирование которых осуществляется с помощью DPM, MABS|
| ProtectedInstanceCount_s |текст |версия 2|Число защищенных экземпляров для связанного элемента резервного копирования или защищенного контейнера в этот момент времени|

### <a name="recoverypoint"></a>RecoveryPoint

В этой таблице представлены основные поля, связанные с точкой восстановления.

| Поле | Тип данных | ОПИСАНИЕ |
| --- | --- | --- |
| BackupItemUniqueId_s |текст |Уникальный идентификатор, используемый для идентификации элемента резервного копирования для виртуальных машин, резервное копирование которых осуществляется с помощью DPM, MABS|
| OldestRecoveryPointTime_s |текст |Дата и время самой старой точки восстановления для элемента резервного копирования|
| OldestRecoveryPointLocation_s |текст |Расположение самой старой точки восстановления для элемента архивации|
| LatestRecoveryPointTime_s |текст |Дата и время последней точки восстановления для элемента резервного копирования|
| LatestRecoveryPointLocation_s |текст |Расположение последней точки восстановления для элемента резервного копирования|

## <a name="next-steps"></a>Дополнительная информация

После просмотра модели данных можно приступить к [созданию пользовательских запросов](../azure-monitor/learn/tutorial-logs-dashboards.md) в журналах Azure Monitor, чтобы создать собственную панель мониторинга.
