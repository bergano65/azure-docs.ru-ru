---
title: Модель журналов данных Azure Monitor
description: В этой статье узнайте о деталях модели данных модели данных Системы мониторинга Azure Monitor Analytics для данных резервного копирования Azure.
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: d14634c5e317682462e77e0549f064c75059f15c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586382"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Модель данных Log Analytics для данных Azure Backup

Используйте модель данных Log Analytics для создания пользовательских оповещений из log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
>
> Эта модель данных отсылает к режиму диагностики Azure для отправки диагностических событий в Log Analytics (LA). Чтобы узнать модель данных для нового конкретного режима ресурса, можно сослаться на следующую статью: [Модель данных для диагностических событий резервного копирования Azure](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

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
|AlertTimeToResolveInMinutes_s    | Number        |Время, задействовававено на устранение оповещения. Пустой для активных оповещений.         |
|AlertConsolidationStatus_s   |Text         |Определить, является ли оповещение консолидированным оповещение или нет         |
|CountOfAlertsConsolidated_s     |Number         |Количество предупреждений, консолидированных, если это консолидированное оповещение          |
|AlertRaisedOn_s     |Text         |Тип сущности, на который поднято оповещение         |
|AlertCode_s     |Text         |Код для однозначного определения типа оповещения         |
|RecommendedAction_s   |Text         |Действия, рекомендованные для устранения оповещения         |
| EventName_s |Text |Имя события. Всегда AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Уникальный идентификатор архивируемого элемента, связанного с предупреждением |
| SchemaVersion_s |Text |Текущая версия схемы, например **V2** |
| State_s |Text |Текущее состояние объекта предупреждения, например "Активный" или "Удален" |
| BackupManagementType_s |Text |Тип поставщика для выполнения архивации, например IaaSVM, FileFolder, к которому относится это предупреждение |
| OperationName |Text |Имя текущей операции, например "Предупреждение" |
| Категория |Text |Категория данных диагностики, отодвинутых в журналы Azure Monitor. Всегда AzureBackupReport |
| Ресурс |Text |Ресурс, для которого собираются данные. Отображается имя хранилища служб восстановления |
| ProtectedContainerUniqueId_s |Text |Уникальный идентификатор защищенного сервера, связанного с оповещением (был ProtectedServerUniqueId_s в V1)|
| VaultUniqueId_s |Text |Уникальный идентификатор защищенного хранилища, связанного с предупреждением |
| SourceSystem |Text |Исходная система текущих данных (Azure) |
| ResourceId |Text |Уникальный идентификатор для ресурса, по которому собираются данные. Например, идентификатор ресурса хранилища восстановления служб |
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
| BackupItemProtectionState_s |Text |Состояние защиты элемента резервного копирования |
| BackupItemAppVersion_s |Text |Версия приложения элемента резервного копирования |
| ProtectionState_s |Text |Текущее состояние защиты архивируемого элемента, например "Защищено" или "Защита остановлена" |
| ProtectionGroupName_s |Text | Название группы защиты, в каком элементе резервного копирования защищено, для SC DPM и MABS, если это применимо|
| SecondaryBackupProtectionState_s |Text |Включена ли вторая защита для элемента резервного копирования|
| SchemaVersion_s |Text |Версия схемы, например, **V2** |
| State_s |Text |Состояние объекта архивируемого элемента, например "Активный" или "Удален" |
| BackupManagementType_s |Text |Тип поставщика для выполнения архивации, например IaaSVM, FileFolder, к которому принадлежит этот архивируемый элемент |
| OperationName |Text |Имя операции, например BackupItem |
| Категория |Text |Категория данных диагностики, отодвинутых в журналы Azure Monitor. Всегда AzureBackupReport |
| Ресурс |Text |Ресурс, для которого собираются данные, например имя хранилища служб восстановления |
| SourceSystem |Text |Исходная система текущих данных (Azure) |
| ResourceId |Text |Идентификатор ресурсов для сбора данных, например, идентификатор ресурсов хранилища восстановления |
| SubscriptionId |Text |Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |Text |Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |Text |Поставщик ресурсов для собираемых данных, например Microsoft.RecoveryServices |
| ResourceType |Text |Тип ресурса для собираемых данных, например "Хранилище" |

### <a name="backupitemassociation"></a>BackupItemAssociation

Эта таблица содержит сведения об ассоциациях архивируемого элемента с различными сущностями.

| Поле | Тип данных | Описание |
| --- | --- | --- |
| EventName_s |Text |Имя события. Это всегда AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Уникальный идентификатор элемента резервного копирования |
| SchemaVersion_s |Text |Это поле обозначает текущую версию схемы, это **V2** |
| State_s |Text |Текущее состояние ассоциации архивируемого элемента, например "Активный" или "Удален" |
| BackupManagementType_s |Text |Тип поставщика для сервера, выполняющего задание резервного копирования, например IaaSVM, FileFolder |
| BackupItemSourceSize_s |Text | Передний конец размера элемента резервного копирования |
| BackupManagementServerUniqueId_s |Text | Поле для однозначной идентификации сервера управления резервным копированием Элемент резервного копирования защищен, если это применимо |
| Категория |Text |Категория данных диагностики, отправляемых в Log Analytics (AzureBackupReport) |
| OperationName |Text |Имя текущей операции (BackupItemAssociation) |
| Ресурс |Text |Ресурс, для которого собираются данные. Отображается имя хранилища служб восстановления |
| ProtectedContainerUniqueId_s |Text |Уникальный идентификатор защищенного сервера, связанный с элементом резервного копирования (был ProtectedServerUniqueId_s в V1) |
| VaultUniqueId_s |Text |Уникальный идентификатор хранилища, которое содержит архивируемый элемент |
| SourceSystem |Text |Исходная система текущих данных (Azure) |
| ResourceId |Text |Идентификатор ресурса для собираемых данных. Например, идентификатор ресурса хранилища «Восстановление служб» |
| SubscriptionId |Text |Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |Text |Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |Text |Поставщик ресурсов для собираемых данных, например Microsoft.RecoveryServices |
| ResourceType |Text |Тип ресурса для данных, которые собираются, например "Хранилище" |

### <a name="backupmanagementserver"></a>Резервное копированиеManagementServer

Эта таблица содержит сведения об ассоциациях архивируемого элемента с различными сущностями.

| Поле | Тип данных | Описание |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |Название сервера управления резервным копированием        |
|AzureBackupAgentVersion_s     |Text         |Версия агента резервного копирования Azure на сервере управления резервным копированием          |
|BackupManagementServerVersion_s     |Text         |Версия сервера управления резервным копированием|
|BackupManagementServerOSVersion_s    |Text            |Версия управления резервным копированием|
|BackupManagementServerType_s     |Text         |Тип сервера управления резервным копированием, как MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Text         |Поле для уникальной идентификации сервера управления резервным копированием       |
| SourceSystem |Text |Исходная система текущих данных (Azure) |
| ResourceId |Text |Идентификатор ресурса для собираемых данных. Например, идентификатор ресурса хранилища «Восстановление служб» |
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
| SchemaVersion_s |Text |Версия схемы, например, **V2** |
| State_s |Text |Текущее состояние объекта задания, например "Активный" или "Удален" |
| BackupManagementType_s |Text |Тип поставщика для сервера, выполняющего задание резервного копирования, например IaaSVM, FileFolder |
| OperationName |Text |Имя текущей операции (Job) |
| Категория |Text |Это поле представляет категорию диагностических данных, отодвинутых в журналы Azure Monitor, это AzureBackupReport |
| Ресурс |Text |Ресурс, для которого собираются данные. Отображается имя хранилища служб восстановления |
| ProtectedServerUniqueId_s |Text |Уникальный идентификатор защищенного сервера, связанного с заданием |
| ProtectedContainerUniqueId_s |Text | Уникальный идентификатор для определения защищенного контейнера, на который работает задание |
| VaultUniqueId_s |Text |Уникальный идентификатор защищенного хранилища |
| JobOperation_s |Text |Операция, для которой запущено задание, например архивация, восстановление, настройка архивации |
| JobStatus_s |Text |Состояние завершенного задания, например "Завершено" или "Ошибка" |
| JobFailureCode_s |Text |Строка кода ошибки, из-за которой произошел сбой задания |
| JobStartDateTime_s |Дата и время |Дата и время начала выполнения задания |
| BackupStorageDestination_s |Text |Место назначения хранилища резервных копий, например облако или диск  |
| AdHocOrScheduledJob_s |Text | Поле для указания, является ли задание Ad Hoc или запланировано |
| JobDurationInSecs_s | Number |Общая длительность задания в секундах |
| DataTransferredInMB_s | Number |Переданные данные (в МБ) для этого задания|
| JobUniqueId_g |Text |Уникальный идентификатор для идентификации задания |
| RecoveryJobDestination_s |Text | Назначение задания восстановления, где данные восстанавливаются |
| RecoveryJobRPDateTime_s |Дата и время | Дата, Время, когда точка восстановления, которая восстанавливается была создана была создана |
| RecoveryJobRPLocation_s |Text | Место, где хранилась точка восстановления, которая восстанавливается|
| SourceSystem |Text |Исходная система текущих данных (Azure) |
| ResourceId |Text |Идентификатор ресурса для собираемых данных. Например, идентификатор ресурса хранилища «Восстановление служб»|
| SubscriptionId |Text |Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |Text |Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |Text |Поставщик ресурсов, для которого собираются данные. Например, Microsoft.RecoveryServices |
| ResourceType |Text |Тип ресурса, для которого собираются данные. Например, "Хранилище" |

### <a name="policy"></a>Политика

Эта таблица содержит сведения о полях, связанных с политикой.

| Поле | Тип данных | Применимые версии | Описание |
| --- | --- | --- | --- |
| EventName_s |Text ||Имя события. Это всегда AzureBackupCentralReport |
| SchemaVersion_s |Text ||Это поле обозначает текущую версию схемы, это **V2** |
| State_s |Text ||Текущее состояние объекта политики, например "Активный" или "Удален" |
| BackupManagementType_s |Text ||Тип поставщика для сервера, выполняющего задание резервного копирования, например IaaSVM, FileFolder |
| OperationName |Text ||Имя текущей операции (Policy) |
| Категория |Text ||Это поле представляет категорию диагностических данных, отодвинутых в журналы Azure Monitor, это AzureBackupReport |
| Ресурс |Text ||Ресурс, для которого собираются данные. Отображается имя хранилища служб восстановления |
| PolicyUniqueId_g |Text ||Уникальный идентификатор для определения политики |
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
| SynchronisationFrequencyPerDay_s |Whole Number |Версия 2|Количество раз в день синхронизация резервного копирования файлов для SC DPM и MABS |
| DiffBackupFormat_s |Text |Версия 2|Формат для дифференциальных резервных копирований для S'L в резервном копировании Azure VM |
| DiffBackupTime_s |Time |Версия 2|Время для дифференциальных резервных копирований для S'L в резервном копировании Azure VM|
| DiffBackupRetentionDuration_s |Десятичное число |Версия 2|Продолжительность хранения для резервного копирования дифференциалов для S'L в резервном копировании Azure VM|
| LogBackupFrequency_s |Десятичное число |Версия 2|Частота резервного копирования журнала для S'L|
| LogBackupRetentionDuration_s |Десятичное число |Версия 2|Продолжительность хранения резервных копий журнала для S'L в резервном копировании Azure VM|
| DiffBackupDaysofTheWeek_s |Text |Версия 2|Дни недели для резервного копирования дифференциальных данных для S'L в резервном копировании Azure VM|
| SourceSystem |Text ||Исходная система текущих данных (Azure) |
| ResourceId |Text ||Идентификатор ресурса для собираемых данных. Например, идентификатор ресурса хранилища «Восстановление служб» |
| SubscriptionId |Text ||Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |Text ||Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |Text ||Поставщик ресурсов, для которого собираются данные. Например, Microsoft.RecoveryServices |
| ResourceType |Text ||Тип ресурса, для которого собираются данные. Например, "Хранилище" |

### <a name="policyassociation"></a>PolicyAssociation

Эта таблица содержит сведения об ассоциациях политики с различными сущностями.

| Поле | Тип данных | Применимые версии | Описание |
| --- | --- | --- | --- |
| EventName_s |Text ||Имя события. Это всегда AzureBackupCentralReport |
| SchemaVersion_s |Text ||Это поле обозначает текущую версию схемы, это **V2** |
| State_s |Text ||Текущее состояние объекта политики, например "Активный" или "Удален" |
| BackupManagementType_s |Text ||Тип поставщика для сервера, выполняющего задание резервного копирования, например IaaSVM, FileFolder |
| OperationName |Text ||Имя текущей операции (PolicyAssociation) |
| Категория |Text ||Это поле представляет категорию диагностических данных, отодвинутых в журналы Azure Monitor, это AzureBackupReport |
| Ресурс |Text ||Ресурс, для которого собираются данные. Отображается имя хранилища служб восстановления |
| PolicyUniqueId_g |Text ||Уникальный идентификатор для определения политики |
| VaultUniqueId_s |Text ||Уникальный идентификатор хранилища, к которому принадлежит эта политика |
| BackupManagementServerUniqueId_s |Text |Версия 2 |Поле для однозначной идентификации сервера управления резервным копированием Элемент резервного копирования защищен, если это применимо        |
| SourceSystem |Text ||Исходная система текущих данных (Azure) |
| ResourceId |Text ||Идентификатор ресурса для собираемых данных. Например, идентификатор ресурса хранилища «Восстановление служб» |
| SubscriptionId |Text ||Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |Text ||Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |Text ||Поставщик ресурсов, для которого собираются данные. Например, Microsoft.RecoveryServices |
| ResourceType |Text ||Тип ресурса, для которого собираются данные. Например, "Хранилище" |

### <a name="protected-container"></a>Защищенный контейнер

В этой таблице приведены основные поля, окоторыханные контейнеры. (Был защищенСервер в v1)

| Поле | Тип данных | Описание |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | Поле для однозначной идентификации защищенного контейнера |
| ProtectedContainerOSType_s |Text |Тип защищенного контейнера ОС |
| ProtectedContainerOSVersion_s |Text |Версия защищенного контейнера Для ОС |
| AgentVersion_s |Text |Номер версии резервного копирования агента или агента защиты (в случае SC DPM и MABS) |
| BackupManagementType_s |Text |Тип поставщика для выполнения резервного копирования. Например, IaaSVM, FileFolder |
| EntityState_s |Text |Текущее состояние защищенного объекта сервера. Например, Активный, Удаленный |
| ProtectedContainerFriendlyName_s |Text |Понятное имя защищенного сервера |
| ProtectedContainerName_s |Text |Название защищенного контейнера |
| ProtectedContainerWorkloadType_s |Text |Тип защищенного контейнера резервного копирования. Например, IaaSVMContainer |
| ProtectedContainerLocation_s |Text |Находится ли защищенный контейнер в помещениях или в Azure |
| ProtectedContainerType_s |Text |Является ли защищенный контейнер сервером или контейнером |
| ProtectedContainerProtectionState_s"  |Text |Состояние защиты защищенного контейнера |

### <a name="storage"></a>Хранилище

Эта таблица содержит сведения о полях, связанных с хранилищем.

| Поле | Тип данных | Описание |
| --- | --- | --- |
| CloudStorageInBytes_s |Десятичное число |Облачное резервное хранилище, используемое резервными копированием, рассчитанное на основе последнего значения (Это поле предназначено только для схемы v1)|
| ProtectedInstances_s |Десятичное число |Число защищенных экземпляров, используемое для вычисления стоимости использования внешнего хранилища для выставления счетов на основе последнего значения |
| EventName_s |Text |Имя события. Это всегда AzureBackupCentralReport |
| SchemaVersion_s |Text |Это поле обозначает текущую версию схемы, это **V2** |
| State_s |Text |Текущее состояние объекта хранилища, например "Активный" или "Удален" |
| BackupManagementType_s |Text |Тип поставщика для сервера, выполняющего задание резервного копирования, например IaaSVM, FileFolder |
| OperationName |Text |Имя текущей операции (Storage) |
| Категория |Text |Это поле представляет категорию диагностических данных, отодвинутых в журналы Azure Monitor, это AzureBackupReport |
| Ресурс |Text |Ресурс, для которого собираются данные. Отображается имя хранилища служб восстановления |
| ProtectedServerUniqueId_s |Text |Уникальный идентификатор защищенного сервера, для которого рассчитывается хранилище |
| VaultUniqueId_s |Text |Рассчитывается уникальный идентификатор хранилища для хранения |
| SourceSystem |Text |Исходная система текущих данных (Azure) |
| ResourceId |Text |Идентификатор ресурса для собираемых данных. Например, идентификатор ресурса хранилища «Восстановление служб» |
| SubscriptionId |Text |Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |Text |Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |Text |Поставщик ресурсов, для которого собираются данные. Например, Microsoft.RecoveryServices |
| ResourceType |Text |Тип ресурса, для которого собираются данные. Например, "Хранилище" |
| StorageUniqueId_s |Text |Уникальный идентификатор, используемый для идентификации объекта хранения данных |
| StorageType_s |Text |Тип хранения, например облако, объем, диск |
| StorageName_s |Text |Наименование единицы хранения, например, E: |
| StorageTotalSizeInGBs_s |Text |Общий размер хранилища в ГБ, потребляемый системой хранения|

### <a name="storageassociation"></a>ХранениеАссоциация

В этой таблице представлены основные поля, связанные с хранением, соединяющие хранилище с другими объектами.

| Поле | Тип данных | Описание |
| --- | --- |  --- |
| StorageUniqueId_s |Text |Уникальный идентификатор, используемый для идентификации объекта хранения данных |
| SchemaVersion_s |Text |Это поле обозначает текущую версию схемы, это **V2** |
| BackupItemUniqueId_s |Text |Уникальный идентификатор, используемый для идентификации элемента резервного копирования, связанного с сущностью хранилища |
| BackupManagementServerUniqueId_s |Text |Уникальный идентификатор, используемый для идентификации сервера управления резервным копированием, связанного с сущностью хранилища|
| VaultUniqueId_s |Text |Уникальный идентификатор, используемый для идентификации хранилища, связанного с объектом хранения|
| StorageConsumedInMBs_s |Number|Размер хранилища, потребляемого соответствующим элементом резервного копирования в соответствующем хранилище |
| StorageAllocatedInMBs_s |Number |Размер хранилища, выделенного соответствующим элементом резервного копирования в соответствующем хранилище типа Disk|

### <a name="vault"></a>Хранилище

Эта таблица содержит сведения о полях, связанных с хранилищем.

| Поле | Тип данных | Описание |
| --- | --- | --- |
| EventName_s |Text |Имя события. Это всегда AzureBackupCentralReport |
| SchemaVersion_s |Text |Это поле обозначает текущую версию схемы, это **V2** |
| State_s |Text |Текущее состояние объекта хранилища, например "Активный" или "Удален" |
| OperationName |Text |Имя текущей операции (Vault) |
| Категория |Text |Это поле представляет категорию диагностических данных, отодвинутых в журналы Azure Monitor, это AzureBackupReport |
| Ресурс |Text |Ресурс, для которого собираются данные. Отображается имя хранилища служб восстановления |
| VaultUniqueId_s |Text |Уникальный идентификатор хранилища |
| VaultName_s |Text |Имя хранилища |
| AzureDataCenter_s |Text |Центр обработки данных, где находится хранилище |
| StorageReplicationType_s |Text |Тип репликации хранилища, например геоизбыточная |
| SourceSystem |Text |Исходная система текущих данных (Azure) |
| ResourceId |Text |Идентификатор ресурса для собираемых данных. Например, идентификатор ресурса хранилища «Восстановление служб» |
| SubscriptionId |Text |Идентификатор подписки ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceGroup |Text |Группа ресурсов ресурса (напр., Хранилище служб восстановления), для которого собираются данные |
| ResourceProvider |Text |Поставщик ресурсов, для которого собираются данные. Например, Microsoft.RecoveryServices |
| ResourceType |Text |Тип ресурса, для которого собираются данные. Например, "Хранилище" |

### <a name="backup-management-server"></a>Сервер управления резервным копированием

В этой таблице приведены основные области серверов управления резервным копированием.

|Поле  |Тип данных  | Описание  |
|---------|---------|----------|
|BackupManagementServerName_s     |Text         |Название сервера управления резервным копированием        |
|AzureBackupAgentVersion_s     |Text         |Версия агента резервного копирования Azure на сервере управления резервным копированием          |
|BackupManagementServerVersion_s     |Text         |Версия сервера управления резервным копированием|
|BackupManagementServerOSVersion_s     |Text            |Версия управления резервным копированием|
|BackupManagementServerType_s     |Text         |Тип сервера управления резервным копированием, как MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Text         |Поле для уникальной идентификации сервера управления резервным копированием       |

### <a name="preferredworkloadonvolume"></a>ПредпочтительнаярабочаянагрузкаТом

В этой таблице опознавательная нагрузка (ы) связана с объемом.

| Поле | Тип данных | Описание |
| --- | --- | --- |
| StorageUniqueId_s |Text |Уникальный идентификатор, используемый для идентификации объекта хранения данных |
| BackupItemType_s |Text |Рабочие нагрузки, для которых этот объем является предпочтительным хранилищем|

### <a name="protectedinstance"></a>ЗащищеноInstance

В этой таблице приведены основные защищенные поля, связанные с экземплярами.

| Поле | Тип данных |Применимые версии | Описание |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |Версия 2|Уникальный идентификатор, используемый для идентификации элемента резервного копирования для вс-карт, резервного копирования с помощью DPM, MABS|
| ProtectedContainerUniqueId_s |Text |Версия 2|Уникальный идентификатор, используемый для идентификации защищенного контейнера для всего, кроме ВМ, резервного копирования с помощью DPM, MABS|
| ProtectedInstanceCount_s |Text |Версия 2|Подсчет защищенных инстанций для связанного элемента резервного копирования или защищенного контейнера в этот момент даты|

### <a name="recoverypoint"></a>RecoveryPoint

В этой таблице приведены основные области, связанные с точкими восстановления.

| Поле | Тип данных | Описание |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |Уникальный идентификатор, используемый для идентификации элемента резервного копирования для вс-карт, резервного копирования с помощью DPM, MABS|
| OldestRecoveryPointTime_s |Text |Время датирования самой старой точки восстановления для элемента резервного копирования|
| OldestRecoveryPointLocation_s |Text |Расположение самой старой точки восстановления для элемента резервного копирования|
| LatestRecoveryPointTime_s |Text |Время датирования последней точки восстановления для элемента резервного копирования|
| LatestRecoveryPointLocation_s |Text |Расположение последней точки восстановления для элемента резервного копирования|

## <a name="sample-kusto-queries"></a>Примеры Кусто Запросы

Ниже приведены несколько примеров, которые помогут вам написать запросы на данных резервного копирования Azure, которые находятся в таблице Azure Diagnostics:

- Все успешные задания резервного копирования

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- Все невыполненные задания резервного копирования

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- Все успешные задания резервного копирования Azure VM

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

- Все успешные задания резервного копирования журнала S'L

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

- Все успешные задания резервного агента Azure

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
    
## <a name="next-steps"></a>Дальнейшие действия

После просмотра модели данных можно начать [создавать пользовательские запросы](../azure-monitor/learn/tutorial-logs-dashboards.md) в журналах Azure Monitor для создания собственной панели мониторинга.
