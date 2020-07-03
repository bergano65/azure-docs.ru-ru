---
title: Модель данных журналов Azure Monitor
description: В этой статье содержатся сведения о Azure Monitor Log Analytics сведения о модели данных для Azure Backup данных.
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 72484923bc94e197cd195c0192b53feb3ef457ce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183693"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Модель данных Log Analytics для данных Azure Backup

Используйте Log Analyticsную модель данных для создания настраиваемых оповещений из Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
>
> Эта модель данных является ссылкой на система диагностики Azure режим отправки диагностических событий в Log Analytics (LA). Чтобы узнать модель данных для нового режима, относящегося к ресурсу, см. следующую статью: [модель данных для Azure Backup событий диагностики](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model) .

## <a name="using-azure-backup-data-model"></a>Использование модели данных Azure Backup

Вы можете использовать следующие поля, предоставляемые как часть модели данных, для создания визуальных элементов, пользовательских запросов и панели мониторинга в соответствии с вашими требованиями.

### <a name="alert"></a>Предупреждение

Эта таблица содержит сведения о полях, связанных с предупреждением.

| Поле | Тип данных | Описание |
| --- | --- | --- |
| AlertUniqueId_s |Text |Уникальный идентификатор созданного предупреждения |
| AlertType_s |Text |Тип предупреждения, например "Резервная копия" |
| AlertStatus_s |Text |Состояние предупреждения, например активное |
| AlertOccurrenceDateTime_s |Дата и время |Дата и время создания предупреждения |
| AlertSeverity_s |Text |Серьезность предупреждения, например критическая |
|AlertTimeToResolveInMinutes_s    | Число        |Время, затраченное на разрешение предупреждения. Пусто для активных оповещений.         |
|AlertConsolidationStatus_s   |Text         |Определяет, является ли предупреждение консолидированным предупреждением         |
|CountOfAlertsConsolidated_s     |Число         |Число консолидированных предупреждений, если это консолидированное предупреждение          |
|AlertRaisedOn_s     |Text         |Тип сущности, на которой создается оповещение         |
|AlertCode_s     |Text         |Код для уникальной идентификации типа оповещения         |
|RecommendedAction_s   |Text         |Рекомендованное действие для разрешения предупреждения         |
| EventName_s |Text |Имя события. Всегда AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Уникальный идентификатор архивируемого элемента, связанного с предупреждением |
| SchemaVersion_s |Text |Текущая версия схемы, например **v2** |
| State_s |Text |Текущее состояние объекта предупреждения, например "Активный" или "Удален" |
| BackupManagementType_s |Text |Тип поставщика для выполнения архивации, например IaaSVM, FileFolder, к которому относится это предупреждение |
| OperationName |Text |Имя текущей операции, например "Предупреждение" |
| Категория |Text |Категория диагностических данных, отправленных в журналы Azure Monitor. Всегда AzureBackupReport |
| Ресурс |Text |Ресурс, для которого собираются данные. Отображается имя хранилища служб восстановления |
| ProtectedContainerUniqueId_s |Text |Уникальный идентификатор защищенного сервера, связанного с предупреждением (был ProtectedServerUniqueId_s в v1)|
| VaultUniqueId_s |Text |Уникальный идентификатор защищенного хранилища, связанного с предупреждением |
| SourceSystem |Text |Исходная система текущих данных (Azure) |
| ResourceId |Text |Уникальный идентификатор для ресурса, по которому собираются данные. Например, идентификатор ресурса хранилища служб восстановления |
| SubscriptionId |Text |Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |Text |Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |Text |Поставщик ресурсов, для которого собираются данные. Например, Microsoft.RecoveryServices |
| ResourceType |Text |Тип ресурса, для которого собираются данные. Например, "Хранилище" |

### <a name="backupitem"></a>BackupItem

Эта таблица содержит сведения о полях, связанных с архивируемым элементом.

| Поле | Тип данных | Описание |
| --- | --- | --- |
| EventName_s |Text |Имя события. Всегда AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Уникальный идентификатор архивируемого элемента |
| BackupItemId_s |Text |Идентификатор элемента резервного копирования (это поле предназначено только для схемы v1) |
| BackupItemName_s |Text |Имя архивируемого элемента |
| BackupItemFriendlyName_s |Text |Понятное имя архивируемого элемента |
| BackupItemType_s |Text |Тип архивируемого элемента, например виртуальная машина, папка с файлами |
| BackupItemProtectionState_s |Text |Состояние защиты элемента архивации |
| BackupItemAppVersion_s |Text |Версия приложения элемента резервного копирования |
| ProtectionState_s |Text |Текущее состояние защиты архивируемого элемента, например "Защищено" или "Защита остановлена" |
| ProtectionGroupName_s |Text | Имя группы защиты, в которой защищен элемент резервного копирования, для SC DPM и MABS, если применимо|
| SecondaryBackupProtectionState_s |Text |Включена ли вторичная Защита для элемента резервного копирования|
| SchemaVersion_s |Text |Версия схемы, например **v2** |
| State_s |Text |Состояние объекта архивируемого элемента, например "Активный" или "Удален" |
| BackupManagementType_s |Text |Тип поставщика для выполнения архивации, например IaaSVM, FileFolder, к которому принадлежит этот архивируемый элемент |
| OperationName |Text |Имя операции, например BackupItem |
| Категория |Text |Категория диагностических данных, отправленных в журналы Azure Monitor. Всегда AzureBackupReport |
| Ресурс |Text |Ресурс, для которого собираются данные, например имя хранилища служб восстановления |
| SourceSystem |Text |Исходная система текущих данных (Azure) |
| ResourceId |Text |Идентификатор ресурса для собираемых данных, например идентификатор ресурса хранилища служб восстановления |
| SubscriptionId |Text |Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |Text |Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |Text |Поставщик ресурсов для собираемых данных, например Microsoft.RecoveryServices |
| ResourceType |Text |Тип ресурса для собираемых данных, например "Хранилище" |

### <a name="backupitemassociation"></a>BackupItemAssociation

Эта таблица содержит сведения об ассоциациях архивируемого элемента с различными сущностями.

| Поле | Тип данных | Описание |
| --- | --- | --- |
| EventName_s |Text |Имя события. Это всегда AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Уникальный идентификатор элемента архивации |
| SchemaVersion_s |Text |Это поле обозначает текущую версию схемы, то есть **v2** |
| State_s |Text |Текущее состояние ассоциации архивируемого элемента, например "Активный" или "Удален" |
| BackupManagementType_s |Text |Тип поставщика для сервера, выполняющего задание резервного копирования, например IaaSVM, FileFolder |
| BackupItemSourceSize_s |Text | Внешний размер элемента резервного копирования |
| BackupManagementServerUniqueId_s |Text | Поле для уникальной идентификации сервера управления архивацией, с которым защищен элемент резервного копирования, если применимо |
| Категория |Text |Категория данных диагностики, отправляемых в Log Analytics (AzureBackupReport) |
| OperationName |Text |Имя текущей операции (BackupItemAssociation) |
| Ресурс |Text |Ресурс, для которого собираются данные. Отображается имя хранилища служб восстановления |
| ProtectedContainerUniqueId_s |Text |Уникальный идентификатор защищенного сервера, связанного с элементом резервного копирования (был ProtectedServerUniqueId_s в v1) |
| VaultUniqueId_s |Text |Уникальный идентификатор хранилища, которое содержит архивируемый элемент |
| SourceSystem |Text |Исходная система текущих данных (Azure) |
| ResourceId |Text |Идентификатор ресурса для собираемых данных. Например, идентификатор ресурса хранилища служб восстановления |
| SubscriptionId |Text |Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |Text |Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |Text |Поставщик ресурсов для собираемых данных, например Microsoft.RecoveryServices |
| ResourceType |Text |Тип ресурса для данных, которые собираются, например "Хранилище" |

### <a name="backupmanagementserver"></a>баккупманажементсервер

Эта таблица содержит сведения об ассоциациях архивируемого элемента с различными сущностями.

| Поле | Тип данных | Описание |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |Имя сервера управления архивацией        |
|AzureBackupAgentVersion_s     |Text         |Версия агента Azure Backup на сервере управления архивацией          |
|BackupManagementServerVersion_s     |Text         |Версия сервера управления архивацией|
|BackupManagementServerOSVersion_s    |Text            |Версия ОС сервера управления архивацией|
|BackupManagementServerType_s     |Text         |Тип сервера управления архивацией, например MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Text         |Поле для уникальной идентификации сервера управления архивацией       |
| SourceSystem |Text |Исходная система текущих данных (Azure) |
| ResourceId |Text |Идентификатор ресурса для собираемых данных. Например, идентификатор ресурса хранилища служб восстановления |
| SubscriptionId |Text |Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |Text |Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |Text |Поставщик ресурсов для собираемых данных, например Microsoft.RecoveryServices |
| ResourceType |Text |Тип ресурса для данных, которые собираются, например "Хранилище" |

### <a name="job"></a>Задание

Эта таблица содержит сведения о полях, связанных с заданием.

| Поле | Тип данных | Описание |
| --- | --- | --- |
| EventName_s |Text |Имя события. Всегда AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Уникальный идентификатор архивируемого элемента |
| SchemaVersion_s |Text |Версия схемы, например **v2** |
| State_s |Text |Текущее состояние объекта задания, например "Активный" или "Удален" |
| BackupManagementType_s |Text |Тип поставщика для сервера, выполняющего задание резервного копирования, например IaaSVM, FileFolder |
| OperationName |Text |Имя текущей операции (Job) |
| Категория |Text |Это поле представляет категорию диагностических данных, отправленных в журналы Azure Monitor. это установите azurebackupreport |
| Ресурс |Text |Ресурс, для которого собираются данные. Отображается имя хранилища служб восстановления |
| ProtectedServerUniqueId_s |Text |Уникальный идентификатор защищенного сервера, связанного с заданием |
| ProtectedContainerUniqueId_s |Text | Уникальный идентификатор для идентификации защищенного контейнера, на котором выполняется задание |
| VaultUniqueId_s |Text |Уникальный идентификатор защищенного хранилища |
| JobOperation_s |Text |Операция, для которой запущено задание, например архивация, восстановление, настройка архивации |
| JobStatus_s |Text |Состояние завершенного задания, например "Завершено" или "Ошибка" |
| JobFailureCode_s |Text |Строка кода ошибки, из-за которой произошел сбой задания |
| JobStartDateTime_s |Дата и время |Дата и время начала выполнения задания |
| BackupStorageDestination_s |Text |Место назначения хранилища резервных копий, например облако или диск  |
| AdHocOrScheduledJob_s |Text | Поле, определяющее, является ли задание нерегламентированным или запланированным |
| JobDurationInSecs_s | Число |Общая длительность задания в секундах |
| DataTransferredInMB_s | Число |Переданные данные (в МБ) для этого задания|
| JobUniqueId_g |Text |Уникальный идентификатор задания |
| RecoveryJobDestination_s |Text | Назначение задания восстановления, в которое восстанавливаются данные |
| RecoveryJobRPDateTime_s |DateTime | Дата и время создания восстанавливаемой точки восстановления |
| RecoveryJobRPLocation_s |Text | Место хранения восстанавливаемой точки восстановления|
| SourceSystem |Text |Исходная система текущих данных (Azure) |
| ResourceId |Text |Идентификатор ресурса для собираемых данных. Например, идентификатор ресурса хранилища служб восстановления|
| SubscriptionId |Text |Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |Text |Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |Text |Поставщик ресурсов, для которого собираются данные. Например, Microsoft.RecoveryServices |
| ResourceType |Text |Тип ресурса, для которого собираются данные. Например, "Хранилище" |

### <a name="policy"></a>Политика

Эта таблица содержит сведения о полях, связанных с политикой.

| Поле | Тип данных | Применимые версии | Описание |
| --- | --- | --- | --- |
| EventName_s |Text ||Имя события. Это всегда AzureBackupCentralReport |
| SchemaVersion_s |Text ||Это поле обозначает текущую версию схемы, то есть **v2** |
| State_s |Text ||Текущее состояние объекта политики, например "Активный" или "Удален" |
| BackupManagementType_s |Text ||Тип поставщика для сервера, выполняющего задание резервного копирования, например IaaSVM, FileFolder |
| OperationName |Text ||Имя текущей операции (Policy) |
| Категория |Text ||Это поле представляет категорию диагностических данных, отправленных в журналы Azure Monitor. это установите azurebackupreport |
| Ресурс |Text ||Ресурс, для которого собираются данные. Отображается имя хранилища служб восстановления |
| PolicyUniqueId_g |Text ||Уникальный идентификатор для идентификации политики |
| PolicyName_s |Text ||Указанное имя политики |
| BackupFrequency_s |Text ||Частота выполнения архивации, например ежедневно или еженедельно |
| BackupTimes_s |Text ||Дата и время запланированной архивации |
| BackupDaysOfTheWeek_s |Text ||Дни недели, на которые запланированы задания архивации |
| RetentionDuration_s |Whole Number ||Длительность хранения настроенных резервных копий |
| DailyRetentionDuration_s |Whole Number ||Общая длительность хранения (в днях) для настроенных резервных копий |
| DailyRetentionTimes_s |Text ||Дата и время настройки ежедневного сохранения |
| WeeklyRetentionDuration_s |Десятичное число ||Общая длительность еженедельного хранения в неделях для настроенных резервных копий |
| WeeklyRetentionTimes_s |Text ||Дата и время настройки еженедельного хранения |
| WeeklyRetentionDaysOfTheWeek_s |Text ||Дни недели, выбранные для еженедельного хранения |
| MonthlyRetentionDuration_s |Десятичное число ||Общая длительность хранения (в месяцах) для настроенных резервных копий |
| MonthlyRetentionTimes_s |Text ||Дата и время настройки ежемесячного хранения |
| MonthlyRetentionFormat_s |Text ||Тип конфигурации для ежемесячного хранения, например ежедневно для хранения по дням, еженедельно для хранения по неделям |
| MonthlyRetentionDaysOfTheWeek_s |Text ||Дни недели, выбранные для ежемесячного хранения |
| MonthlyRetentionWeeksOfTheMonth_s |Text ||Недели месяца, настроенные для ежемесячного хранения, например первая, последняя и т. д. |
| YearlyRetentionDuration_s |Десятичное число ||Общая длительность хранения (в годах) для настроенных резервных копий |
| YearlyRetentionTimes_s |Text ||Дата и время настройки ежегодного хранения |
| YearlyRetentionMonthsOfTheYear_s |Text ||Месяцы года, выбранные для ежегодного хранения |
| YearlyRetentionFormat_s |Text ||Тип конфигурации для ежегодного хранения, например ежедневно для хранения по дням, еженедельно для хранения по неделям | |
| YearlyRetentionDaysOfTheMonth_s |Text ||Дни месяца, выбранные для ежегодного хранения |
| SynchronisationFrequencyPerDay_s |Whole Number |Версия 2|Число раз в день синхронизации резервной копии файлов для SC DPM и MABS |
| DiffBackupFormat_s |Text |Версия 2|Формат разностных резервных копий для SQL в службе архивации виртуальных машин Azure |
| DiffBackupTime_s |Время |Версия 2|Время для разностных резервных копий SQL в службе архивации виртуальных машин Azure|
| DiffBackupRetentionDuration_s |Десятичное число |Версия 2|Продолжительность хранения разностных резервных копий для SQL в службе архивации виртуальных машин Azure|
| LogBackupFrequency_s |Десятичное число |Версия 2|Периодичность резервного копирования журналов для SQL|
| LogBackupRetentionDuration_s |Десятичное число |Версия 2|Продолжительность хранения резервных копий журналов для SQL в службе архивации виртуальных машин Azure|
| DiffBackupDaysofTheWeek_s |Text |Версия 2|Дни недели для разностных резервных копий SQL в службе архивации виртуальных машин Azure|
| SourceSystem |Text ||Исходная система текущих данных (Azure) |
| ResourceId |Text ||Идентификатор ресурса для собираемых данных. Например, идентификатор ресурса хранилища служб восстановления |
| SubscriptionId |Text ||Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |Text ||Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |Text ||Поставщик ресурсов, для которого собираются данные. Например, Microsoft.RecoveryServices |
| ResourceType |Text ||Тип ресурса, для которого собираются данные. Например, "Хранилище" |

### <a name="policyassociation"></a>PolicyAssociation

Эта таблица содержит сведения об ассоциациях политики с различными сущностями.

| Поле | Тип данных | Применимые версии | Описание |
| --- | --- | --- | --- |
| EventName_s |Text ||Имя события. Это всегда AzureBackupCentralReport |
| SchemaVersion_s |Text ||Это поле обозначает текущую версию схемы, то есть **v2** |
| State_s |Text ||Текущее состояние объекта политики, например "Активный" или "Удален" |
| BackupManagementType_s |Text ||Тип поставщика для сервера, выполняющего задание резервного копирования, например IaaSVM, FileFolder |
| OperationName |Text ||Имя текущей операции (PolicyAssociation) |
| Категория |Text ||Это поле представляет категорию диагностических данных, отправленных в журналы Azure Monitor. это установите azurebackupreport |
| Ресурс |Text ||Ресурс, для которого собираются данные. Отображается имя хранилища служб восстановления |
| PolicyUniqueId_g |Text ||Уникальный идентификатор для идентификации политики |
| VaultUniqueId_s |Text ||Уникальный идентификатор хранилища, к которому принадлежит эта политика |
| BackupManagementServerUniqueId_s |Text |Версия 2 |Поле для уникальной идентификации сервера управления архивацией, с которым защищен элемент резервного копирования, если применимо        |
| SourceSystem |Text ||Исходная система текущих данных (Azure) |
| ResourceId |Text ||Идентификатор ресурса для собираемых данных. Например, идентификатор ресурса хранилища служб восстановления |
| SubscriptionId |Text ||Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |Text ||Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |Text ||Поставщик ресурсов, для которого собираются данные. Например, Microsoft.RecoveryServices |
| ResourceType |Text ||Тип ресурса, для которого собираются данные. Например, "Хранилище" |

### <a name="protected-container"></a>Защищенный контейнер

В этой таблице приводятся основные поля о защищенных контейнерах. (Было ProtectedServerо в v1)

| Поле | Тип данных | Описание |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | Поле для уникальной идентификации защищенного контейнера |
| ProtectedContainerOSType_s |Text |Тип ОС защищенного контейнера |
| ProtectedContainerOSVersion_s |Text |Версия ОС защищенного контейнера |
| AgentVersion_s |Text |Номер версии агента резервного копирования или агента защиты (в случае с SC DPM и MABS) |
| BackupManagementType_s |Text |Тип поставщика для выполнения резервного копирования. Например, IaaSVM, FileFolder |
| EntityState_s |Text |Текущее состояние объекта защищенного сервера. Например, "активный", "удален" |
| ProtectedContainerFriendlyName_s |Text |Понятное имя защищенного сервера |
| ProtectedContainerName_s |Text |Имя защищенного контейнера |
| ProtectedContainerWorkloadType_s |Text |Тип защищенного контейнера, резервная копия которого была создана. Например, Иаасвмконтаинер |
| ProtectedContainerLocation_s |Text |Находится ли защищенный контейнер в локальной среде или в Azure |
| ProtectedContainerType_s |Text |Является ли защищенный контейнер сервером или контейнером |
| ProtectedContainerProtectionState_s "  |Text |Состояние защиты защищенного контейнера |

### <a name="storage"></a>Память

Эта таблица содержит сведения о полях, связанных с хранилищем.

| Поле | Тип данных | Описание |
| --- | --- | --- |
| CloudStorageInBytes_s |Десятичное число |Хранилище облачных резервных копий, используемое резервными копиями, рассчитанное на основе последнего значения (это поле предназначено только для схемы v1)|
| ProtectedInstances_s |Десятичное число |Число защищенных экземпляров, используемое для вычисления стоимости использования внешнего хранилища для выставления счетов на основе последнего значения |
| EventName_s |Text |Имя события. Это всегда AzureBackupCentralReport |
| SchemaVersion_s |Text |Это поле обозначает текущую версию схемы, то есть **v2** |
| State_s |Text |Текущее состояние объекта хранилища, например "Активный" или "Удален" |
| BackupManagementType_s |Text |Тип поставщика для сервера, выполняющего задание резервного копирования, например IaaSVM, FileFolder |
| OperationName |Text |Имя текущей операции (Storage) |
| Категория |Text |Это поле представляет категорию диагностических данных, отправленных в журналы Azure Monitor. это установите azurebackupreport |
| Ресурс |Text |Ресурс, для которого собираются данные. Отображается имя хранилища служб восстановления |
| ProtectedServerUniqueId_s |Text |Уникальный идентификатор защищенного сервера, для которого вычисляется хранилище |
| VaultUniqueId_s |Text |Будет рассчитан уникальный идентификатор хранилища для хранилища |
| SourceSystem |Text |Исходная система текущих данных (Azure) |
| ResourceId |Text |Идентификатор ресурса для собираемых данных. Например, идентификатор ресурса хранилища служб восстановления |
| SubscriptionId |Text |Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |Text |Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |Text |Поставщик ресурсов, для которого собираются данные. Например, Microsoft.RecoveryServices |
| ResourceType |Text |Тип ресурса, для которого собираются данные. Например, "Хранилище" |
| StorageUniqueId_s |Text |Уникальный идентификатор, используемый для идентификации сущности хранилища |
| StorageType_s |Text |Тип хранилища, например облако, том, диск |
| StorageName_s |Text |Имя сущности хранилища, например E:\ |
| StorageTotalSizeInGBs_s |Text |Общий размер хранилища в ГБ, потребляемый сущностью хранилища|

### <a name="storageassociation"></a>сторажеассоЦиатион

В этой таблице представлены основные связанные с хранилищем поля, связывающие хранилище с другими сущностями.

| Поле | Тип данных | Описание |
| --- | --- |  --- |
| StorageUniqueId_s |Text |Уникальный идентификатор, используемый для идентификации сущности хранилища |
| SchemaVersion_s |Text |Это поле обозначает текущую версию схемы, то есть **v2** |
| BackupItemUniqueId_s |Text |Уникальный идентификатор, используемый для идентификации элемента резервного копирования, связанного с сущностью хранилища |
| BackupManagementServerUniqueId_s |Text |Уникальный идентификатор, используемый для идентификации сервера управления архивацией, связанного с сущностью хранилища|
| VaultUniqueId_s |Text |Уникальный идентификатор, используемый для идентификации хранилища, связанного с сущностью хранилища|
| StorageConsumedInMBs_s |Число|Размер хранилища, используемого соответствующим элементом резервного копирования в соответствующем хранилище |
| StorageAllocatedInMBs_s |Число |Размер хранилища, выделенного соответствующим элементом резервного копирования в соответствующем хранилище типа "диск"|

### <a name="vault"></a>Хранилище

Эта таблица содержит сведения о полях, связанных с хранилищем.

| Поле | Тип данных | Описание |
| --- | --- | --- |
| EventName_s |Text |Имя события. Это всегда AzureBackupCentralReport |
| SchemaVersion_s |Text |Это поле обозначает текущую версию схемы, то есть **v2** |
| State_s |Text |Текущее состояние объекта хранилища, например "Активный" или "Удален" |
| OperationName |Text |Имя текущей операции (Vault) |
| Категория |Text |Это поле представляет категорию диагностических данных, отправленных в журналы Azure Monitor. это установите azurebackupreport |
| Ресурс |Text |Ресурс, для которого собираются данные. Отображается имя хранилища служб восстановления |
| VaultUniqueId_s |Text |Уникальный идентификатор хранилища |
| VaultName_s |Text |Имя хранилища |
| AzureDataCenter_s |Text |Центр обработки данных, где находится хранилище |
| StorageReplicationType_s |Text |Тип репликации хранилища, например геоизбыточная |
| SourceSystem |Text |Исходная система текущих данных (Azure) |
| ResourceId |Text |Идентификатор ресурса для собираемых данных. Например, идентификатор ресурса хранилища служб восстановления |
| SubscriptionId |Text |Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |Text |Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |Text |Поставщик ресурсов, для которого собираются данные. Например, Microsoft.RecoveryServices |
| ResourceType |Text |Тип ресурса, для которого собираются данные. Например, "Хранилище" |

### <a name="backup-management-server"></a>Сервер управления архивацией

В этой таблице приводятся основные поля, посвященные серверам управления архивацией.

|Поле  |Тип данных  | Описание  |
|---------|---------|----------|
|BackupManagementServerName_s     |Text         |Имя сервера управления архивацией        |
|AzureBackupAgentVersion_s     |Text         |Версия агента Azure Backup на сервере управления архивацией          |
|BackupManagementServerVersion_s     |Text         |Версия сервера управления архивацией|
|BackupManagementServerOSVersion_s     |Text            |Версия ОС сервера управления архивацией|
|BackupManagementServerType_s     |Text         |Тип сервера управления архивацией, например MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Text         |Поле для уникальной идентификации сервера управления архивацией       |

### <a name="preferredworkloadonvolume"></a>преферредворклоадонволуме

В этой таблице указаны рабочие нагрузки, с которыми связан том.

| Поле | Тип данных | Описание |
| --- | --- | --- |
| StorageUniqueId_s |Text |Уникальный идентификатор, используемый для идентификации сущности хранилища |
| BackupItemType_s |Text |Рабочие нагрузки, для которых этот том является предпочтительным хранилищем|

### <a name="protectedinstance"></a>протектединстанце

В этой таблице представлены базовые связанные с защищенными экземплярами поля.

| Поле | Тип данных |Применимые версии | Описание |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |Версия 2|Уникальный идентификатор, используемый для идентификации элемента резервного копирования для виртуальных машин, резервное копирование которых осуществляется с помощью DPM, MABS|
| ProtectedContainerUniqueId_s |Text |Версия 2|Уникальный идентификатор, используемый для идентификации защищенного контейнера для всех, кроме виртуальных машин, резервное копирование которых осуществляется с помощью DPM, MABS|
| ProtectedInstanceCount_s |Text |Версия 2|Число защищенных экземпляров для связанного элемента резервного копирования или защищенного контейнера в этот момент времени|

### <a name="recoverypoint"></a>RecoveryPoint

В этой таблице представлены основные поля, связанные с точкой восстановления.

| Поле | Тип данных | Описание |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |Уникальный идентификатор, используемый для идентификации элемента резервного копирования для виртуальных машин, резервное копирование которых осуществляется с помощью DPM, MABS|
| OldestRecoveryPointTime_s |Text |Дата и время самой старой точки восстановления для элемента резервного копирования|
| OldestRecoveryPointLocation_s |Text |Расположение самой старой точки восстановления для элемента архивации|
| LatestRecoveryPointTime_s |Text |Дата и время последней точки восстановления для элемента резервного копирования|
| LatestRecoveryPointLocation_s |Text |Расположение последней точки восстановления для элемента резервного копирования|

## <a name="sample-kusto-queries"></a>Примеры запросов Kusto

Ниже приведено несколько примеров, помогающих создавать запросы к Azure Backup данным, находящиеся в система диагностики Azure таблице.

- Все успешные задания резервного копирования

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- Все задания резервного копирования, завершившиеся сбоем

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- Все успешные задания резервного копирования виртуальных машин Azure

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

- Все успешные задания резервного копирования журналов SQL

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

- Все успешные задания агента Azure Backup

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

## <a name="v1-schema-vs-v2-schema"></a>Схема версии v1 схемы VS v2
Ранее данные диагностики для агента Azure Backup и резервной копии виртуальной машины Azure были отправлены в система диагностики Azure таблицу в схеме, называемой ***схемой v1***. Впоследствии были добавлены новые столбцы для поддержки других сценариев и рабочих нагрузок, а диагностические данные были помещены в новую схему, называемую ***схемой v2***. 

По соображениям обратной совместимости диагностические данные для Azure Backup агента и резервной копии виртуальной машины Azure в настоящее время отправляются в система диагностики Azureную таблицу в схеме v1 и v2 (с схемой v1 в качестве пути устаревания). Можно выбрать, какие записи в Log Analytics имеют схему v1, путем фильтрации записей для SchemaVersion_s = = "v1" в запросах журнала.

## <a name="next-steps"></a>Дальнейшие действия

После просмотра модели данных можно приступить к [созданию пользовательских запросов](../azure-monitor/learn/tutorial-logs-dashboards.md) в журналах Azure Monitor, чтобы создать собственную панель мониторинга.
