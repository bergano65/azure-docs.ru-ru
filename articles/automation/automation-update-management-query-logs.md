---
title: Журналы управления обновлением Запроса Azure
description: В этой статье описывается, как запросить журналы для управления обновлениями в рабочем пространстве Журнала Analytics.
services: automation
ms.subservice: update-management
ms.date: 03/11/2020
ms.topic: conceptual
ms.openlocfilehash: f31168d47f31d8e740c95cb3d9e449f473cc78dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79216848"
---
# <a name="query-update-records-for-update-management-in-azure-monitor-logs"></a>Записи обновления запросов для управления обновлениями в журналах Azure Monitor

В дополнение к деталям, приведенным в решении Update Management, можно высматривать журналы, хранящиеся в рабочей области Log Analytics. На странице решения, в левом стеле выберите **журналы**. Открывается страница **поиска журнала.**

Вы также можете узнать, как настроить запросы или использовать их от разных клиентов и многое другое, посетив: [Log Analytics поиск API документации](https://dev.loganalytics.io/).

## <a name="update-records"></a>Записи обновлений

Записи, собранные управлением обновлениями для Windows и Linux VMs и типами данных, которые отображаются в результатах поиска журналов. В следующих разделах описывают эти записи.

### <a name="required-updates"></a>Необходимые обновления

`RequiredUpdate` Создается запись с типом типа, представляющая обновления, требуемые машиной. У этих записей есть свойства, приведенные в таблице ниже.

| Свойство | Описание | 
|----------|-------------|
| Компьютер | Полностью квалифицированное доменное имя машины отчетности. |
| KBID | Идентификатор базы знаний для обновления Windows. |
| ManagementGroupName | Название группы управления менеджером операций или рабочего пространства Log Analytics. | 
| Продукт | Продукты, для которых обновление применимо. | 
| PublishDate | Дата, когда обновление готово к загрузке и установке из Windows Update. |
| Сервер | | 
| SourceHealthServiceId | Уникальный идентификатор, представляющий идентификатор агента Log Analytics Windows. |
| SourceSystem | *OperationsManager* | 
| TenantId | Уникальный идентификатор, представляющий экземпляр Active Directory Azure. | 
| TimeGenerated | Дата и время создания записи | 
| Тип | *Обновление* | 
| UpdateClassification | Указывает тип обновлений, которые могут быть применены. Для Windows:<br> *Критические обновления*<br> *Обновления безопасности*<br> *накопительные пакеты обновления;*<br> *пакеты дополнительных компонентов;*<br> *Сервисные пакеты*<br> *обновления определений;*<br> *Сервис*<br> *Обновления*. Для Linux:<br> *критические обновления и обновления для системы безопасности;*<br> *Прочее* |
| ОбновлениеSeverity | Оценка степени тяжести уязвимости. Возможны следующие значения.<br> *Критических*<br> *Важно.*<br> *Средний*<br> *Низкий* |
| UpdateTitle | Название обновления.|

### <a name="update"></a>Update

`Update` Создается запись с типом типа, представляющая доступные обновления и состояние их установки для машины. У этих записей есть свойства, приведенные в таблице ниже.

| Свойство | Описание | 
|----------|-------------|
| УтверждениеИсточник | Применяется только к операционной системе Windows. Значение *— обновление Майкрософт*. |
| Approved | *Правда* или *ложь* |
| Классификация | *Обновления* |
| Компьютер | Полностью квалифицированное доменное имя машины отчетности. |
| Компьютернаясреда | *Лазурный* или *не-Azure*. |
| MSRCBulletinID | Идентификационный номер бюллетеня безопасности | 
| MSRCSeverity | Оценка степени тяжести уязвимости. Возможны следующие значения.<br> *Критических*<br> *Важно.*<br> *Средний*<br> *Низкий* |  
| KBID | Идентификатор базы знаний для обновления Windows. |
| ManagementGroupName | Название группы управления менеджером операций или рабочего пространства Log Analytics. |
| UpdateID | Уникальный идентификатор обновления программного обеспечения. |
| RevisionNumber | Номер пересмотра конкретного пересмотра обновления. |
| Необязательный | *Правда* или *ложь* | 
| RebootBehavior | Поведение перезагрузки после установки/установки обновления. |
| _ResourceId | Уникальный идентификатор для ресурса, с которым связана запись. |
| Тип | *Обновление* |
| ВМУУИД | Уникальный идентификатор для виртуальной машины. |
| MG | Уникальный идентификатор для группы управления или рабочего пространства Log Analytics. | 
| TenantId | Уникальный идентификатор, представляющий экземпляр Active Directory Azure. | 
| SourceSystem | *OperationsManager* | 
| TimeGenerated | Дата и время создания записи | 
| SourceComputerId | Уникальный идентификатор, представляющий исходный компьютер. | 
| Заголовок | Название обновления. |
| ОпубликованоДата (UTC) | Дата, когда обновление готово к загрузке и установке из Windows Update.  |
| UpdateState | Текущее состояние обновления. | 
| Продукт | Продукты, для которых обновление применимо. |
| SubscriptionId | Уникальный идентификатор подписки Azure. | 
| ResourceGroup | Название ресурсной группы, в которого входит ресурс. | 
| ResourceProvider | Определяет поставщика ресурсов. | 
| Ресурс | Имя ресурса. | 
| ResourceType | Имя типа ресурса. | 

### <a name="update-agent"></a>Обновление агента

`UpdateAgent` Создается запись с типом, которая предоставляет подробную информацию об агенте обновления на машине. У этих записей есть свойства, приведенные в таблице ниже.

| Свойство | Описание | 
|----------|-------------|
| AgeofOldestMissingRequiredUpdate | | 
| AutomaticUpdateEnabled | | 
| Компьютер | Полностью квалифицированное доменное имя машины отчетности. |
| DaySinceLastUpdateBucket | | 
| ManagementGroupName | Название группы управления менеджером операций или рабочего пространства Log Analytics. |
| OSVersion | Версия операционной системы. |
| Сервер | |
| SourceHealthServiceId | Уникальный идентификатор, представляющий идентификатор агента Log Analytics Windows. |
| SourceSystem | *OperationsManager* | 
| TenantId | Уникальный идентификатор, представляющий экземпляр Active Directory Azure. |
| TimeGenerated | Дата и время создания записи |
| Тип | *Обновление* | 
| WindowsUpdateAgentVersion | Версия агента обновления Windows. |
| WSUSServer | Отображает ошибки, если у агента обновления Windows есть проблема с устранением неполадок. |

### <a name="update-deployment-status"></a>Состояние развертывания обновления 

`UpdateRunProgress` Создается запись с типом типа, обеспечивающая состояние развертывания обновлений запланированного развертывания машиной. У этих записей есть свойства, приведенные в таблице ниже.

| Свойство | Описание | 
|----------|-------------|
| Компьютер | Полностью квалифицированное доменное имя машины отчетности. |
| Компьютернаясреда | *Лазурный* или *не-Azure*. | 
| CorrelationId | Уникальный идентификатор задания runbook для обновления. |
| EndTime | Время завершения процесса синхронизации. | 
| ОшибкаРезультат | Код ошибки обновления Windows генерируется, если обновление не удается установить. | 
| УстановкаСтатус | Возможные состояния установки обновления на клиентском *компьютере, В процессе*, *Успешно*, Частично *не удалось*. |
| KBID | Идентификатор базы знаний для обновления Windows. | 
| ManagementGroupName | Название группы управления менеджером операций или рабочего пространства Log Analytics. |
| OSType | Определяет тип операционной системы, *Windows* или *Linux.* | 
| Продукт | Продукты, для которых обновление применимо. |
| Ресурс | Имя ресурса. | 
| ResourceId | Уникальный идентификатор для ресурса, с которым связана запись. |
| ResourceProvider | Определяет поставщика ресурсов. | 
| ResourceType | Имя типа ресурса. | 
| SourceComputerId | Уникальный идентификатор, представляющий исходный компьютер. | 
| SourceSystem | *OperationsManager* |
| StartTime | Время, когда обновление планируется установить. |
| SubscriptionId | Уникальный идентификатор подписки Azure. | 
| ПреуспеваютНаретрия | Отображается, когда выполнение обновления не удалось с первой попытки, а текущая операция — это попытка повторной попытки. |
| TimeGenerated | Дата и время создания записи |
| Заголовок | Название обновления. |
| Тип | *UpdateRunProgress* |
| ОбновлениеId | Уникальный идентификатор обновления программного обеспечения. |
| ВМУУИД | Уникальный идентификатор для виртуальной машины. |
| _ResourceId | Уникальный идентификатор для ресурса, с которым связана запись. |

### <a name="update-summary"></a>Обновить сводку 

`UpdateSummary` Создается запись с типом типа, которая обеспечивает резюме обновления по машине. У этих записей есть свойства, приведенные в таблице ниже.

| Свойство | Описание | 
|----------|-------------|
| Компьютер | Полностью квалифицированное доменное имя машины отчетности. |
| Компьютернаясреда | *Лазурный* или *не-Azure*. | 
| CriticalUpdatesMissing | Количество критических обновлений, не достающихся, которые применимы. | 
| ManagementGroupName | Название группы управления менеджером операций или рабочего пространства Log Analytics. |
| NETRuntimeVersion | Версия рамочного соглашения .NET, установленная на компьютере Windows. |
| OldestMissingSecurityUpdateBucket | Возможны следующие значения.<br> *Последние*<br> *30 дней назад*<br> *60 дней назад*<br> *Более старая* | 
| OldestMissingSecurityUpdateInDays | Общее количество дней для старейшего обновления, обнаруженного как применимое, которое не было установлено. |
| OsVersion | Версия операционной системы. |
| OtherUpdatesMissing | Количество обнаруженных обновлений отсутствует. |
| Ресурс |  Имя ресурса. | 
| ResourceGroup | Название ресурсной группы, в которого входит ресурс. |
| ResourceId | Уникальный идентификатор для ресурса, с которым связана запись. |
| ResourceProvider | Определяет поставщика ресурсов. |
| ResourceType | Имя типа ресурса. |
| ПерезагрузкаОжиданиа | *Правда* или *ложь*. |
| SecurityUpdatesMissing | Количество недостающих обновлений безопасности применимо.| 
| SourceComputerId | Уникальный идентификатор для виртуальной машины. |
| SourceSystem | *OpsManager* | 
| SubscriptionId | Уникальный идентификатор подписки Azure. |
| TimeGenerated | Дата и время создания записи |
| TotalUpdatesMissing | Общее количество недостающих обновлений применимо. | 
| Тип | *UpdateSummary* |
| ВМУУИД | Уникальный идентификатор для виртуальной машины. |
| WindowsUpdateAgentVersion | Версия агента обновления Windows. |
| WindowsUpdateSetting | Отображает состояние агента обновления Windows. Возможны следующие значения:<br> *Плановая установка*<br> *Уведомить перед установкой*<br> Ошибка возвращается из нездорового агента WUA. | 
| WSUSServer | Отображает ошибки, если у агента обновления Windows есть проблема с устранением неполадок. |
| _ResourceId | Уникальный идентификатор для ресурса, с которым связана запись. |

## <a name="sample-queries"></a>Примеры запросов

В следующих разделах приводятся примеры запросов журналов для записей обновлений, которые собираются для управления обновлением.

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Подтверждение подключения виртуальных машин, не относящихся к Azure

Чтобы подтвердить, что непосредственно подключенные машины связываются с журналами Azure Monitor, запустите один из следующих поисков журналов.

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

На компьютере Windows можно просмотреть следующую информацию для проверки подключения агента к журналам Azure Monitor:

1. На панели управления откройте **Microsoft Monitoring Agent**. На вкладке **Azure Log Analytics** агент отобразит сообщение: **The Microsoft Monitoring Agent has successfully connected to Log Analytics** (Microsoft Monitoring Agent успешно подключен к Log Analytics).
2. Откройте журнал событий Windows. Перейдите к **журналам приложения и служб или Operations Manager** и выполните поиск идентификатора события 3000 или 5002 в исходном **соединителе службы**. Эти события указывают, что компьютер был зарегистрирован для рабочей области Log Analytics и получает конфигурации.

Если агент не может общаться с журналами Azure Monitor и агент настроен для связи с Интернетом через брандмауэр или прокси-сервер, подтвердите правильно настроенный брандмауэр или прокси-сервер. Чтобы узнать, как проверить это, ознакомьтесь со статьей [Подключение компьютеров Windows к службе Log Analytics в Azure](../azure-monitor/platform/agent-windows.md) или [Настройка агента Log Analytics для компьютеров Linux в гибридной среде](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Если при подключении этого решения системы Linux настроены для взаимодействия с прокси-сервером или шлюзом Log Analytics, обновите разрешения *proxy.conf*, чтобы предоставить группе omiuser разрешение на чтение файла. Для этого выполните следующие команды:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Добавленные агенты Linux отобразят состояние **обновления** после оценки. Этот процесс может занять до 6 часов.

Чтобы подтвердить, что группа управления операциями общается с журналами Azure Monitor, см. [интеграцию менеджера по операциям validate Operations Ис с журналами Azure Monitor.](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor)

### <a name="single-azure-vm-assessment-queries-windows"></a>Запросы оценки одной виртуальной машины Azure (Windows)

Замените значение VMUUID на GUID запрашиваемой виртуальной машины. Вы можете найти VMUUID, который следует использовать при запуске следующего запроса в журналах Azure Monitor:`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

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

Для некоторых дистросов Linux существует несоответствие [endianness](https://en.wikipedia.org/wiki/Endianness) значению VMUUID, которое исходит от менеджера ресурсов Azure и тем, что хранится в журналах Azure Monitor. Следующий запрос проверяет совпадение по порядку байтов. Чтобы вернулись правильные результаты, замените значения VMUUID форматом GUID с прямым и обратным порядком. Вы можете найти VMUUID, который следует использовать при запуске следующего запроса в журналах Azure Monitor:`Update | where Computer == "<machine name>"
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

## <a name="next-steps"></a>Дальнейшие действия

* Используйте поиск журналов в [журналах Azure Monitor](../log-analytics/log-analytics-log-searches.md) для просмотра подробных данных обновления.
* [Создавайте оповещения](automation-tutorial-update-management.md#configure-alerts) о состоянии развертывания обновлений.
