---
title: Запрос журналов Управление обновлениями Azure
description: В этой статье описывается, как запрашивать журналы для Управление обновлениями в рабочей области Log Analytics.
services: automation
ms.subservice: update-management
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: 09eacb42eff6ecf3a3fca2d7fb401f52195f5f2d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617418"
---
# <a name="query-update-records-for-update-management-in-azure-monitor-logs"></a>Запросы на обновление записей для Управление обновлениями в журналах Azure Monitor

Помимо сведений, предоставленных в решении Управление обновлениями, можно выполнять поиск по журналам, хранящимся в рабочей области Log Analytics. На странице решения в области слева выберите **журналы**. Откроется страница поиска по журналам.

Вы также можете научиться настраивать запросы или использовать их с разных клиентов. См. [документацию по API log Analytics поиска](https://dev.loganalytics.io/).

## <a name="update-records"></a>Записи обновлений

Управление обновлениями собирает записи для виртуальных машин Windows и Linux и типы данных, которые отображаются в результатах поиска по журналам. Эти записи описаны в следующих разделах.

### <a name="required-updates"></a>Необходимые обновления

Создается запись с типом `RequiredUpdate` , которая представляет обновления, необходимые для компьютера. У этих записей есть свойства, приведенные в таблице ниже.

| Свойство | Описание | 
|----------|-------------|
| Компьютер | Полное доменное имя компьютера для создания отчетов. |
| KBID | Идентификатор статьи базы знаний для центра обновления Windows. |
| ManagementGroupName | Имя Operations Manager группы управления или Log Analytics рабочей области. | 
| Продукт | Продукты, для которых применимо обновление. | 
| PublishDate | Дата готовности обновления к загрузке и установке с Центр обновления Windows. |
| Server (Сервер) | | 
| SourceHealthServiceId | Уникальный идентификатор, представляющий идентификатор агента Log Analytics Windows. |
| SourceSystem | *OperationsManager* | 
| TenantId | Уникальный идентификатор, представляющий экземпляр Azure Active Directory организации. | 
| TimeGenerated | Дата и время создания записи | 
| Type | *Обновление* | 
| UpdateClassification | Указывает тип обновлений, которые могут быть применены. Для Windows:<br> *критические обновления;*<br> *Обновления для системы безопасности*<br> *накопительные пакеты обновления;*<br> *пакеты дополнительных компонентов;*<br> *пакеты обновления;*<br> *обновления определений;*<br> *Инструменты*<br> *Обновления*. Для Linux:<br> *критические обновления и обновления для системы безопасности;*<br> *Прочее* |
| упдатесеверити | Оценка серьезности уязвимости. Возможны следующие значения.<br> *Critical*<br> *Важно.*<br> *Средний*<br> *Низкий* |
| UpdateTitle | Заголовок обновления.|

### <a name="update"></a>Обновление:

Создается запись с типом `Update` , которая представляет доступные обновления и их состояние установки для компьютера. У этих записей есть свойства, приведенные в таблице ниже.

| Свойство | Описание | 
|----------|-------------|
| аппровалсаурце | Применяется только к операционной системе Windows. Источник утверждения для записи. Значение Центр обновления Майкрософт. |
| Approved | Значение true, если запись утверждена, или false в противном случае. |
| Классификация | Классификация утверждения. Значение — Updates. |
| Компьютер | Полное доменное имя компьютера для создания отчетов. |
| компутеренвиронмент | PXE. Возможные значения: Azure или не Azure. |
| MSRCBulletinID | ИДЕНТИФИКАЦИОНный номер бюллетеня по безопасности. | 
| MSRCSeverity | Оценка серьезности уязвимости. Возможны следующие значения.<br> Critical<br> Важно!<br> Средний<br> Низкий |  
| KBID | Идентификатор статьи базы знаний для центра обновления Windows. |
| ManagementGroupName | Имя Operations Manager группы управления или Log Analytics рабочей области. |
| UpdateID | Уникальный идентификатор обновления программного обеспечения. |
| RevisionNumber | Номер редакции определенной редакции обновления. |
| Необязательный | Значение true, если запись является необязательной, или false в противном случае. | 
| RebootBehavior | Поведение при перезагрузке после установки или удаления обновления. |
| _ResourceId | Уникальный идентификатор ресурса, связанного с записью. |
| Type | Тип записи. Значение — Update. |
| вмууид | Уникальный идентификатор виртуальной машины. |
| MG | Уникальный идентификатор для группы управления или рабочей области Log Analytics. | 
| TenantId | Уникальный идентификатор, представляющий экземпляр Azure Active Directory вашей организации. | 
| SourceSystem | Исходная система для записи. Значение равно `OperationsManager`. | 
| TimeGenerated | Дата и время создания записи. | 
| SourceComputerId | Уникальный идентификатор, представляющий исходный компьютер. | 
| Название | Заголовок обновления. |
| Публишеддате (UTC) | Дата, когда обновление будет готово к загрузке и установке с Центр обновления Windows.  |
| UpdateState | Текущее состояние обновления. | 
| Продукт | Продукты, для которых применимо обновление. |
| SubscriptionId | Уникальный идентификатор подписки Azure. | 
| ResourceGroup | Имя группы ресурсов, которой принадлежит ресурс. | 
| ResourceProvider | Поставщики ресурсов. | 
| Ресурс | Имя ресурса. | 
| ResourceType | Тип ресурса. | 

### <a name="update-agent"></a>Обновление агента

Создается запись с типом `UpdateAgent` , которая предоставляет сведения об агенте обновления на компьютере. У этих записей есть свойства, приведенные в таблице ниже.

| Свойство | Описание | 
|----------|-------------|
| ажеофолдестмиссингрекуиредупдате | | 
| AutomaticUpdateEnabled | | 
| Компьютер | Полное доменное имя компьютера для создания отчетов. |
| дайсинцеластупдатебуккет | | 
| ManagementGroupName | Имя Operations Manager группы управления или Log Analytics рабочей области. |
| OSVersion | Версия операционной системы. |
| Server (Сервер) | |
| SourceHealthServiceId | Уникальный идентификатор, представляющий идентификатор агента Log Analytics Windows. |
| SourceSystem | Исходная система для записи. Значение равно `OperationsManager`. | 
| TenantId | Уникальный идентификатор, представляющий экземпляр Azure Active Directory вашей организации. |
| TimeGenerated | Дата и время создания записи. |
| Type | Тип записи. Значение — Update. | 
| WindowsUpdateAgentVersion | Версия агента Центр обновления Windows. |
| WSUSServer | Ошибки, если у агента Центр обновления Windows возникла проблема, чтобы помочь в устранении неполадок. |

### <a name="update-deployment-status"></a>Состояние развертывания обновления 

Создается запись с типом `UpdateRunProgress` , которая предоставляет состояние развертывания обновлений для запланированного развертывания по компьютеру. У этих записей есть свойства, приведенные в таблице ниже.

| Свойство | Описание | 
|----------|-------------|
| Компьютер | Полное доменное имя компьютера для создания отчетов. |
| компутеренвиронмент | PXE. Значения: Azure или не Azure. | 
| CorrelationId | Уникальный идентификатор запуска задания Runbook для обновления. |
| EndTime | Время завершения процесса синхронизации. | 
| еррорресулт | Центр обновления Windows код ошибки, создаваемый при сбое установки обновления. | 
| инсталлатионстатус | Возможные состояния установки обновления на клиентском компьютере;<br> `NotStarted`-Задание еще не активировано.<br> `FailedToStart`-не удалось запустить задание на компьютере.<br> `Failed`-задание запущено, но завершилось с исключением.<br> `InProgress`— Задание выполняется.<br> `MaintenanceWindowExceeded`— Если выполнение осталось, но достигнут интервал периода обслуживания.<br> `Succeeded`— Задание прошло.<br> `InstallFailed`-не удалось успешно установить обновление.<br> `NotIncluded`<br> `Excluded` |
| KBID | Идентификатор статьи базы знаний для центра обновления Windows. | 
| ManagementGroupName | Имя Operations Manager группы управления или Log Analytics рабочей области. |
| OSType | Тип операционной системы. Значения — Windows или Linux. | 
| Продукт | Продукты, для которых применимо обновление. |
| Ресурс | Имя ресурса. | 
| ResourceId | Уникальный идентификатор ресурса, связанного с записью. |
| ResourceProvider | Поставщики ресурсов. | 
| ResourceType | Тип ресурса. | 
| SourceComputerId | Уникальный идентификатор, представляющий исходный компьютер. | 
| SourceSystem | Исходная система для записи. Значение равно `OperationsManager`. |
| StartTime | Время, когда обновление планируется установить. |
| SubscriptionId | Уникальный идентификатор подписки Azure. | 
| сукцеедедонретри | Значение, указывающее, произошла ли ошибка при первой попытке выполнения обновления, и текущая операция является повторной попыткой. |
| TimeGenerated | Дата и время создания записи. |
| Название | Заголовок обновления. |
| Type | Тип обновления. Значение равно `UpdateRunProgress`. |
| UpdateId | Уникальный идентификатор обновления программного обеспечения. |
| вмууид | Уникальный идентификатор виртуальной машины. |
| ResourceId | Уникальный идентификатор ресурса, связанного с записью. |

### <a name="update-summary"></a>Обновить сводку 

Создается запись с типом `UpdateSummary` , которая предоставляет сводку обновлений по компьютеру. У этих записей есть свойства, приведенные в таблице ниже.

| Свойство | Описание | 
|----------|-------------|
| Компьютер | Полное доменное имя компьютера для создания отчетов. |
| компутеренвиронмент | PXE. Значения: Azure или не Azure. | 
| CriticalUpdatesMissing | Число отсутствующих применимых критических обновлений. | 
| ManagementGroupName | Имя Operations Manager группы управления или Log Analytics рабочей области. |
| NETRuntimeVersion | Версия .NET Framework, установленного на компьютере Windows. |
| OldestMissingSecurityUpdateBucket | Описатель самого раннего отсутствующего контейнера безопасности. Возможны следующие значения.<br> Последнее, если значение меньше 30 дней<br> 30 дней назад<br> 60 дней назад<br> 90 дней назад<br> 120 дней назад<br> 150 дней назад<br> 180 дней назад<br> Более ранний, если значение больше 180 дней. | 
| OldestMissingSecurityUpdateInDays | Общее количество дней, в течение которых обнаружено самое старое обновление, которое не было установлено. |
| OsVersion | Версия операционной системы. |
| OtherUpdatesMissing | Число отсутствующих обнаруженных обновлений. |
| Ресурс | Имя ресурса для записи. | 
| ResourceGroup | Имя группы ресурсов, содержащей ресурс. |
| ResourceId | Уникальный идентификатор ресурса, связанного с записью. |
| ResourceProvider | Поставщики ресурсов. |
| ResourceType | Тип ресурса. |
| рестартпендинг | Значение true, если перезапуск находится в состоянии ожидания, или false в противном случае. |
| SecurityUpdatesMissing | Число применимых обновлений для системы безопасности.| 
| SourceComputerId | Уникальный идентификатор виртуальной машины. |
| SourceSystem | Исходная система для записи. Значение равно `OpsManager`. | 
| SubscriptionId | Уникальный идентификатор подписки Azure. |
| TimeGenerated | Дата и время создания записи. |
| TotalUpdatesMissing | Общее число применимых обновлений. | 
| Type | Тип записи. Значение равно `UpdateSummary`. |
| вмууид | Уникальный идентификатор виртуальной машины. |
| WindowsUpdateAgentVersion | Версия агента Центр обновления Windows. |
| WindowsUpdateSetting | Состояние агента Центр обновления Windows. Возможны следующие значения:<br> `Scheduled installation`<br> `Notify before installation`<br> `Error returned from unhealthy WUA agent` | 
| WSUSServer | Ошибки, если у агента Центр обновления Windows возникла проблема, чтобы помочь в устранении неполадок. |
| _ResourceId | Уникальный идентификатор ресурса, связанного с записью. |

## <a name="sample-queries"></a>Примеры запросов

В следующих разделах приведены примеры запросов журналов для записей обновления, собираемых для Управление обновлениями.

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Подтверждение подключения виртуальных машин, не относящихся к Azure

Чтобы убедиться, что непосредственно подключенные компьютеры взаимодействуют с журналами Azure Monitor, выполните один из следующих операций поиска по журналам.

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

На компьютере Windows можно ознакомиться со следующими сведениями, чтобы проверить подключение агента к журналам Azure Monitor.

1. На панели управления откройте **Microsoft Monitoring Agent**. На вкладке **Azure Log Analytics** агент отобразит сообщение: **The Microsoft Monitoring Agent has successfully connected to Log Analytics** (Microsoft Monitoring Agent успешно подключен к Log Analytics).
2. Откройте журнал событий Windows. Перейдите к **журналам приложения и служб или Operations Manager** и выполните поиск идентификатора события 3000 или 5002 в исходном **соединителе службы**. Эти события указывают, что компьютер был зарегистрирован для рабочей области Log Analytics и получает конфигурации.

Если агент не может взаимодействовать с журналами Azure Monitor и агент настроен для взаимодействия с Интернетом через брандмауэр или прокси-сервер, убедитесь, что брандмауэр или прокси-сервер настроен правильно. Чтобы узнать, как проверить это, ознакомьтесь со статьей [Подключение компьютеров Windows к службе Log Analytics в Azure](../azure-monitor/platform/agent-windows.md) или [Настройка агента Log Analytics для компьютеров Linux в гибридной среде](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Если при подключении этого решения системы Linux настроены для взаимодействия с прокси-сервером или шлюзом Log Analytics, обновите разрешения *proxy.conf*, чтобы предоставить группе omiuser разрешение на чтение файла. Для этого выполните следующие команды:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Добавленные агенты Linux отобразят состояние **обновления** после оценки. Этот процесс может занять до 6 часов.

Чтобы убедиться, что Группа управления Operations Manager взаимодействует с журналами Azure Monitor, см. раздел [Проверка интеграции Operations Manager с журналами Azure Monitor](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor).

### <a name="single-azure-vm-assessment-queries-windows"></a>Запросы оценки одной виртуальной машины Azure (Windows)

Замените значение VMUUID на GUID запрашиваемой виртуальной машины. Вы можете найти ВМУУИД, который следует использовать, выполнив следующий запрос в журналах Azure Monitor:`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Сводка об отсутствующих обновлениях

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Список отсутствующих обновлений

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

### <a name="single-azure-vm-assessment-queries-linux"></a>Запросы оценки одной виртуальной машины Azure (Linux)

Для некоторых дистрибутивов Linux существует несоответствие [порядок следования байтов](https://en.wikipedia.org/wiki/Endianness) со значением вмууид, которое берется из Azure Resource Manager и что хранится в журналах Azure Monitor. Следующий запрос проверяет совпадение по порядку байтов. Чтобы вернулись правильные результаты, замените значения VMUUID форматом GUID с прямым и обратным порядком. Вы можете найти ВМУУИД, который следует использовать, выполнив следующий запрос в журналах Azure Monitor:`Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Сводка об отсутствующих обновлениях

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Список отсутствующих обновлений

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

### <a name="multi-vm-assessment-queries"></a>Запросы для оценки нескольких виртуальных машин

#### <a name="computers-summary"></a>Сводка по компьютерам

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount
```

#### <a name="missing-updates-summary"></a>Сводка об отсутствующих обновлениях

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="computers-list"></a>Список компьютеров

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

#### <a name="missing-updates-list"></a>Список отсутствующих обновлений

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="next-steps"></a>Дальнейшие шаги

* Используйте поиск по журналам в [Azure Monitor журналов](../log-analytics/log-analytics-log-searches.md) для просмотра подробных сведений об обновлениях.
* [Создавайте оповещения](automation-tutorial-update-management.md#configure-alerts) о состоянии развертывания обновлений.
