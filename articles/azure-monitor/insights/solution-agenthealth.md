---
title: Работоспособность агентов решение в Azure Monitor | Документация Майкрософт
description: Эта статья поможет вам понять, как использовать это решение для отслеживания работоспособности агентов, которые отправляют отчеты непосредственно в Log Analytics или System Center Operations Manager.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/19/2017
ms.openlocfilehash: cbeaa3e148d6fbe20d7ddb4d04cd00d6300f9818
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75402443"
---
#  <a name="agent-health-solution-in-azure-monitor"></a>Работоспособность агентов решение в Azure Monitor
Решение Работоспособность агентов в Azure помогает понять, что для всех агентов, отправляющих отчеты непосредственно в рабочую область Log Analytics в Azure Monitor или группу управления System Center Operations Manager, подключенную к Azure Monitor, которая не отвечает и идет отправка рабочих данных.  Вы также можете отслеживать число развернутых агентов и их географическое распределение, а также выполнять другие запросы, чтобы знать о распределении агентов, развернутых в Azure, других облачных средах или в локальной среде.    

## <a name="prerequisites"></a>Технические условия
Перед развертыванием этого решения убедитесь, что у вас есть поддерживаемые [агенты Windows](../../log-analytics/log-analytics-windows-agent.md), которые отправляют отчеты в рабочую область Log Analytics или [группу управления Operations Manager](../../azure-monitor/platform/om-agents.md), интегрированную с вашей рабочей областью.

## <a name="solution-components"></a>Компоненты решения
Это решение состоит из следующих ресурсов, добавленных в вашу рабочую область, и подключенных напрямую агентов или подключенной группы управления Operations Manager.

### <a name="management-packs"></a>Пакеты управления
Если группа управления System Center Operations Manager подключена к рабочей области Log Analytics, в Operations Manager будут установлены следующие пакеты.  После добавления этого решения пакеты управления также будут установлены на компьютерах с Windows, подключенных напрямую. Управление и настройка здесь не требуются.

* Пакет аналитики канала Direct оценки работоспособности помощника Microsoft System Center (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Пакет аналитики канала сервера оценки работоспособности помощника Microsoft System Center (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

Дополнительные сведения об обновлении пакетов управления для решений см. в статье [Подключение Operations Manager к Log Analytics](../../azure-monitor/platform/om-agents.md).

## <a name="configuration"></a>Настройка
Добавьте решение для мониторинга работоспособности агентов в рабочую область Log Analytics, как описано в статье [Добавление решений](solutions.md). Дополнительная настройка не требуется.


## <a name="data-collection"></a>сбор данных
### <a name="supported-agents"></a>Поддерживаемые агенты
В следующей таблице описаны подключенные источники, которые поддерживаются этим решением.

| Подключенный источник | Поддерживается | Description |
| --- | --- | --- |
| Агенты Windows | Да | События пульса собираются от прямых агентов Windows.|
| Группа управления System Center Operations Manager | Да | События пульса собираются от агентов, отправляющих отчеты в группу управления каждые 60 секунд, а затем пересылаются в Azure Monitor. Прямое подключение от агентов Operations Manager к Azure Monitor не требуется. Данные события пульса пересылаются из группы управления в рабочую область Log Analytics.|

## <a name="using-the-solution"></a>Использование решения
При добавлении решения в рабочую область Log Analytics на панель мониторинга добавляется плитка **Работоспособность агентов**. На ней отображается общее число агентов и число агентов, не отвечающих на запросы за последние 24 часа.<br><br> ![Плитка решения "Работоспособность агентов" на панели мониторинга](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

Щелкните плитку **Работоспособность агентов**, чтобы открыть панель мониторинга **Работоспособность агентов**.  Панель мониторинга содержит столбцы, перечисленные в приведенной ниже таблице. Каждый столбец содержит по десять основных событий, соответствующих таким указанным критериям, как диапазон времени. Можно выполнить поиск по журналам, чтобы отобразить весь список. Для этого щелкните элемент **Показать все** в правой нижней части каждого столбца или заголовок этого столбца.

| Столбец | Description |
|--------|-------------|
| Число агентов по времени | Тенденция изменения числа агентов в течение семи дней для агентов Linux и Windows.|
| Число агентов, не отвечающих на запросы | Список агентов, которые не отправили пакеты пульса за последние 24 часа.|
| Распределение по типам ОС | Число агентов Windows и Linux в вашей среде.|
| Распределение по версиям агентов | Другие версии агентов, установленные в вашей среде, и их число.|
| Распределение по категориям агентов | Различные категории агентов, отправляющих события пульса: прямые агенты, агенты OpsMgr и сервер управления OpsMgr.|
| Распределение по группам управления | Раздел различных групп управления Operations Manager в вашей среде.|
| Географическое расположение агентов | Раздел различных стран и регионов, в которых имеются агенты и общее количество агентов, установленных в каждой стране или регионе.|
| Число установленных шлюзов | Число серверов, на которых установлен шлюз Log Analytics, и список этих серверов.|

![Пример панели мониторинга "Работоспособность агентов"](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Записи журнала Azure Monitor
Решение создает один тип записи в рабочей области Log Analytics.  

### <a name="heartbeat-records"></a>Записи пульсов
Создается запись с типом **Пульс**.  У этих записей есть свойства, приведенные в таблице ниже.  

| Свойство | Description |
| --- | --- |
| `Type` | *Пульс*|
| `Category` | Значение — *Прямой агент*, *Агент SCOM*, или *Сервер управления SCOM*.|
| `Computer` | Имя компьютера.|
| `OSType` | Операционная система Windows или Linux.|
| `OSMajorVersion` | Основная версия операционной системы.|
| `OSMinorVersion` | Второстепенная версия операционной системы.|
| `Version` | Версия агента Log Analytics или агента Operations Manager.|
| `SCAgentChannel` | Значение — *Прямой* и (или) *SCManagementServer*.|
| `IsGatewayInstalled` | Если шлюз Log Analytics установлен, этот параметр имеет значение *true*, в противном случае — значение *false*.|
| `ComputerIP` | IP-адрес компьютера.|
| `RemoteIPCountry` | Географическое расположение, в котором развернут компьютер.|
| `ManagementGroupName` | Имя группы управления Operations Manager.|
| `SourceComputerId` | Уникальный идентификатор компьютера.|
| `RemoteIPLongitude` | Долгота географического расположения компьютера.|
| `RemoteIPLatitude` | Широта географического расположения компьютера.|

Каждый агент, передающий данные на сервер управления Operations Manager, будет передавать два пульса, а значение свойства SCAgentChannel будет включать **прямые** и **скманажементсервер** в зависимости от того, какие источники данных и решения мониторинга включены в подписку. При отзыве данные из решений отправляются непосредственно с сервера управления Operations Manager на Azure Monitor или из-за объема данных, собираемых агентом, передаются непосредственно из агента в Azure Monitor. Для событий пульса со значением **SCManagementServer** значением ComputerIP является IP-адрес сервера управления, так как он фактически передает данные.  Для пульсов со значением **Прямой** для параметра SCAgentChannel ComputerIP — это общедоступный IP-адрес агента.  

## <a name="sample-log-searches"></a>Пример поисков журналов
Следующая таблица содержит примеры поисков по журналу для получения записей, собранных этим решением.

| Запрос | Description |
|:---|:---|
| Heartbeat &#124; distinct Computer |Общее число агентов |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |Число агентов, не отвечающих на запросы за последние 24 часа |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |Число агентов, не отвечающих на запросы за последние 15 минут |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Подключенные компьютеры (за последние 24 часа) |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Общее число автономных агентов за последние 30 минут (за последние 24 часа) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Получение тенденции изменения числа агентов за промежуток времени по типу ОС|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Распределение по типам ОС |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Распределение по версиям агентов |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Распределение по категориям агентов |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Распределение по группам управления |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |Географическое расположение агентов |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Число установленных шлюзов Log Analytics |




## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о создании оповещений из запросов журналов см. [в Azure Monitor](../platform/alerts-overview.md) . 
