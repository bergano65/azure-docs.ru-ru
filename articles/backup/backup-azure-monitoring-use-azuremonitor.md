---
title: Мониторинг резервного копирования Azure с помощью Azure Monitor
description: Мониторинг рабочих нагрузок резервного копирования Azure и создание пользовательских оповещений с помощью Azure Monitor.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 547cef66be9902468f4e2755c31e5f586eccad5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79459520"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Мониторинг в масштабе с помощью Azure Monitor

Azure Backup предоставляет [встроенные возможности мониторинга и оповещения](backup-azure-monitoring-built-in-monitor.md) в хранилище Служб восстановления. Эти возможности доступны без какой бы то ни было дополнительной инфраструктуры управления. Но эта встроенная услуга ограничена в следующих сценариях:

- Если вы отслеживаете данные из нескольких хранилищ служб восстановления по подписке
- Если предпочтительный канал уведомлений *не* является электронной почтой
- Если пользователям нужны оповещения для большего количества сценариев
- Если вы хотите просмотреть информацию из находинного компонента, такого как менеджер по защите данных System Center в Azure, который портал не отображается в резервных [**вакансиях**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) или [**оповещениях о резервном копировании**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Использование рабочего пространства анализа журналов

### <a name="create-alerts-by-using-log-analytics"></a>Создавайте оповещения с помощью аналитики журналов

В Azure Monitor можно создавать собственные оповещения в рабочей области analytics log. В рабочем пространстве используются *группы действий Azure* для выбора предпочтительного механизма уведомления.

> [!IMPORTANT]
> Для получения информации о стоимости [Azure Monitor pricing](https://azure.microsoft.com/pricing/details/monitor/)создания этого запроса см.

Откройте раздел **журналов** рабочего пространства Log Analytics и создайте запрос для собственных журналов. При выборе **нового правила оповещения**открывается страница создания оповещения Azure Monitor, как показано на следующем изображении.

![Создание оповещения в рабочем пространстве Analytics журнала](media/backup-azure-monitoring-laworkspace/custom-alert.png)

Здесь ресурс уже помечен как рабочее пространство Log Analytics, и предусмотрена интеграция групп действий.

![Страница создания оповещения Log Analytics](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Состояние оповещения

Определяющей характеристикой оповещения является его инициирующее состояние. Выберите **Условие** для автоматической загрузки запроса Kusto на странице **журналов,** как показано на следующем изображении. Здесь вы можете отсеить условие в соответствии с вашими потребностями. Для получения дополнительной [информации см.](#sample-kusto-queries)

![Настройка состояния оповещения](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

При необходимости можно отсватить запрос Kusto. Выберите порог, период и частоту. Порог определяет, когда оповещение будет поднято. Период — это окно времени выполнения запроса. Например, если порог превышает 0, период составляет 5 минут, а частота составляет 5 минут, то правило запускает запрос каждые 5 минут, просматривая предыдущие 5 минут. Если количество результатов превышает 0, вы будете уведомлены через выбранную группу действий.

#### <a name="alert-action-groups"></a>Группы действий оповещения

Используйте группу действий для указания канала уведомлений. Чтобы увидеть доступные механизмы уведомления в **группах Action,** выберите **Создать новый**.

![Доступные механизмы уведомления в окне "Добавить группу действий"](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Вы можете удовлетворить все требования по предупреждению и мониторингу только от Log Analytics, или вы можете использовать Log Analytics в дополнение к встроенным уведомлениям.

Для получения дополнительной информации [см. Создание, представление и управление оповещениями о журналах с помощью Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) и [создания и управления группами действий на портале Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

### <a name="sample-kusto-queries"></a>Примеры запросов Кусто

Графики по умолчанию дают вам kusto запросы для основных сценариев, на которых можно создавать оповещения. Вы также можете изменить запросы, чтобы получить данные, которые вы хотите быть предупреждены. Вставьте следующий пример запросов Kusto на странице **журналов,** а затем создайте оповещения по запросам:

- Все успешные задания резервного копирования

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    ````

- Все невыполненные задания резервного копирования

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Failed"
    ````

- Все успешные задания резервного копирования Azure VM

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="VM" and BackupManagementType=="IaaSVM"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Все успешные задания резервного копирования журнала S'L

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup" and JobOperationSubType=="Log"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="SQLDataBase" and BackupManagementType=="AzureWorkload"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Все успешные задания резервного агента Azure

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="FileFolder" and BackupManagementType=="MAB"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Резервное хранение, потребляемые по элементу резервного копирования

    ````Kusto
    CoreAzureBackup
    //Get all Backup Items
    | where OperationName == "BackupItem"
    //Get distinct Backup Items
    | distinct BackupItemUniqueId, BackupItemFriendlyName
    | join kind=leftouter
    (AddonAzureBackupStorage
    | where OperationName == "StorageAssociation"
    //Get latest record for each Backup Item
    | summarize arg_max(TimeGenerated, *) by BackupItemUniqueId 
    | project BackupItemUniqueId , StorageConsumedInMBs)
    on BackupItemUniqueId
    | project BackupItemUniqueId , BackupItemFriendlyName , StorageConsumedInMBs 
    | sort by StorageConsumedInMBs desc
    ````


### <a name="diagnostic-data-update-frequency"></a>Частота обновления диагностических данных

Диагностические данные из хранилища перекачиваются в рабочее пространство Log Analytics с некоторым лагом. Каждое событие поступает в рабочее пространство Log Analytics через *20-30 минут* после того, как оно будет вытеснено из хранилища служб восстановления. Вот более подробная информация об отставании:

- Во всех решениях встроенные оповещения службы резервного копирования отодвигаются сразу после их создания. Таким образом, они обычно появляются в рабочей области журнала Analytics через 20-30 минут.
- Во всех решениях, по требованию резервного копирования рабочих мест и восстановления рабочих мест толкают, как только они *заканчиваются.*
- Для всех решений, за исключением резервного копирования, запланированные задания резервного копирования толкаются сразу же после *их завершения.*
- Для резервного копирования ссистемы резервного копирования, поскольку резервное копирование журнала может происходить каждые 15 минут, информация обо всех выполненных заданиях резервного копирования, включая журналы, будет оттачиваться и выталкиваться каждые 6 часов.
- Во всех решениях другая информация, такая как элемент резервного копирования, политика, точки восстановления, хранилище и т.д., толкается по крайней мере *один раз в день.*
- Изменение конфигурации резервного копирования (например, изменение политики или политика редактирования) вызывает нажатие всей связанной информации о резервном копировании.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Использование журналов активности хранилища служб восстановления

> [!CAUTION]
> Следующие шаги применяются только к *резервным капотам Azure VM.* Эти действия нельзя использовать для таких решений, как резервное копирование Azure, резервные ожидания в Azure или файлы Azure.

Вы также можете использовать журналы активности для получения уведомлений о таких событиях, как успех резервного копирования. Для начала выполните следующие действия:

1. Войдите на портал Azure.
1. Откройте хранилище соответствующих служб восстановления.
1. В свойствах хранилища откройте раздел **журнала Activity.**

Чтобы определить соответствующий журнал и создать оповещение:

1. Убедитесь, что вы получаете журналы активности для успешного резервного копирования, применяя фильтры, отображаемые на следующем изображении. Изменение значения **Timespan** по мере необходимости для просмотра записей.

   ![Фильтрация для поиска журналов активности для резервных копий Azure VM](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. Выберите имя операции, чтобы увидеть соответствующие детали.
1. Выберите **новое правило оповещения,** чтобы открыть страницу **правила «Создание».**
1. Создайте оповещение, выполняя следующие действия в [журнале Create, view and manage, используя Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

   ![Новое правило генерации оповещений](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Здесь ресурс представляет собой хранилище служб восстановления. Повторите одни и те же шаги для всех хранилищ, в которых вы хотите получить уведомления через журналы активности. Условие не будет иметь порог, период или частоту, потому что это предупреждение основано на событиях. Как только генерируется соответствующий журнал активности, оповещение повышается.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Использование аналитики журналов для мониторинга в масштабе

Вы можете просматривать все оповещения, созданные из журналов активности и рабочих областей аналитики журналов в Azure Monitor. Просто откройте панель **оповещения** слева.

Хотя вы можете получать уведомления через журналы активности, мы настоятельно рекомендуем использовать журналы журналов журналов журналов log Analytics, а не журналы активности для мониторинга в масштабе. Далее описывается, почему это происходит:

- **Ограниченные сценарии**: Уведомления через журналы активности применяются только к резервным копиям Azure VM. Уведомления должны быть настроены для каждого хранилища служб восстановления.
- **Определение подходит**: Запланированное действие резервного копирования не соответствует последнему определению журналов активности. Вместо этого он выравнивается с [журналами ресурсов.](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#what-you-can-do-with-platform-logs-in-a-workspace) Это выравнивание вызывает неожиданные эффекты при изменении данных, протекаемых через канал журнала активности.
- **Проблемы с каналом журнала активности:** В хранилищах служб восстановления журналы активности, которые закачиваются из резервного копирования Azure, следуют новой модели. К сожалению, это изменение влияет на генерацию журналов активности в правительстве Azure, Azure ВГермании и Azure China 21Vianet. Если пользователи этих облачных служб создают или настраивали оповещения из журналов активности в Azure Monitor, оповещения не срабатывают. Кроме того, во всех общедоступных регионах Azure, если пользователь [собирает журналы активности восстановления в рабочее пространство Analytics log,](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)эти журналы не отображаются.

Используйте рабочее пространство журнала Analytics для мониторинга и оповещения в масштабе для всех рабочих нагрузок, защищенных резервным копированием Azure.

## <a name="next-steps"></a>Дальнейшие действия

Для создания пользовательских [Log Analytics data model](backup-azure-reports-data-model.md)запросов см.
