---
title: Решение для мониторинга работоспособности агентов в Azure | Документы Майкрософт
description: Эта статья поможет вам понять, как использовать это решение для отслеживания работоспособности агентов, которые отправляют отчеты непосредственно в Log Analytics или System Center Operations Manager.
services: operations-management-suite
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2017
ms.author: magoedte
ms.openlocfilehash: f0737c6a6ff228b92a030242faf7f4d634bdd9f2
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45733196"
---
#  <a name="agent-health-solution-in-azure"></a>Решение для мониторинга работоспособности агентов в Azure
Решение для мониторинга работоспособности агентов в Azure помогает выявлять среди всех агентов, отправляющих отчеты непосредственно в Log Analytics или подключенную к Log Analytics группу управления System Center Operations Manager, агенты, которые не отвечают на запросы и которые отправляют оперативные данные.  Вы также можете отслеживать число развернутых агентов и их географическое распределение, а также выполнять другие запросы, чтобы знать о распределении агентов, развернутых в Azure, других облачных средах или в локальной среде.    

## <a name="prerequisites"></a>Предварительные требования
Перед развертыванием этого решения убедитесь, что у вас есть поддерживаемые [агенты Windows](../log-analytics/log-analytics-windows-agent.md), которые отправляют отчеты в рабочую область Log Analytics или [группу управления Operations Manager](../log-analytics/log-analytics-om-agents.md), интегрированную с вашей рабочей областью.    

## <a name="solution-components"></a>Компоненты решения
Это решение состоит из следующих ресурсов, добавленных в вашу рабочую область, и подключенных напрямую агентов или подключенной группы управления Operations Manager.

### <a name="management-packs"></a>Пакеты управления
Если группа управления System Center Operations Manager подключена к рабочей области Log Analytics, в Operations Manager будут установлены следующие пакеты.  После добавления этого решения пакеты управления также будут установлены на компьютерах с Windows, подключенных напрямую. Управление и настройка здесь не требуются.

* Пакет аналитики канала Direct оценки работоспособности помощника Microsoft System Center (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Пакет аналитики канала сервера оценки работоспособности помощника Microsoft System Center (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

Дополнительные сведения об обновлении пакетов управления для решений см. в статье [Подключение Operations Manager к Log Analytics](../log-analytics/log-analytics-om-agents.md).

## <a name="configuration"></a>Параметр Configuration
Добавьте решение для мониторинга работоспособности агентов в рабочую область Log Analytics, как описано в статье [Добавление решений](../log-analytics/log-analytics-add-solutions.md). Дополнительная настройка не требуется.


## <a name="data-collection"></a>Сбор данных
### <a name="supported-agents"></a>Поддерживаемые агенты
В следующей таблице описаны подключенные источники, которые поддерживаются этим решением.

| Подключенный источник | Поддерживаются | ОПИСАНИЕ |
| --- | --- | --- |
| Агенты Windows | Yes | События пульса собираются от прямых агентов Windows.|
| Группа управления System Center Operations Manager | Yes | События пульса собираются с агентов, предоставляющих отчеты группам управления, каждые 60 секунд, а затем перенаправляются в Log Analytics. Прямое подключение агентов Operations Manager к Log Analytics не требуется. Данные событий пульса перенаправляются из группы управления в репозиторий Log Analytics.|

## <a name="using-the-solution"></a>Использование решения
При добавлении решения в рабочую область Log Analytics на панель мониторинга добавляется плитка **Работоспособность агентов**. На ней отображается общее число агентов и число агентов, не отвечающих на запросы за последние 24 часа.<br><br> ![Плитка решения "Работоспособность агентов" на панели мониторинга](./media/monitoring-solution-agenthealth/agenthealth-solution-tile-homepage.png)

Щелкните плитку **Работоспособность агентов**, чтобы открыть панель мониторинга **Работоспособность агентов**.  Панель мониторинга содержит столбцы, перечисленные в приведенной ниже таблице. Каждый столбец содержит по десять основных событий, соответствующих таким указанным критериям, как диапазон времени. Можно выполнить поиск по журналам, чтобы отобразить весь список. Для этого щелкните элемент **Показать все** в правой нижней части каждого столбца или заголовок этого столбца.

| столбец | ОПИСАНИЕ |
|--------|-------------|
| Число агентов по времени | Тенденция изменения числа агентов в течение семи дней для агентов Linux и Windows.|
| Число агентов, не отвечающих на запросы | Список агентов, которые не отправили пакеты пульса за последние 24 часа.|
| Распределение по типам ОС | Число агентов Windows и Linux в вашей среде.|
| Распределение по версиям агентов | Другие версии агентов, установленные в вашей среде, и их число.|
| Распределение по категориям агентов | Различные категории агентов, отправляющих события пульса: прямые агенты, агенты OpsMgr и сервер управления OpsMgr.|
| Распределение по группам управления | Разные группы управления SCOM в вашей среде.|
| Географическое расположение агентов | Разные страны, в которых находятся ваши агенты, и общее число агентов, установленных в каждой стране.|
| Число установленных шлюзов | Число серверов, на которых установлен шлюз Log Analytics, и список этих серверов.|

![Пример панели мониторинга "Работоспособность агентов"](./media/monitoring-solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="log-analytics-records"></a>Записи Log Analytics
Решение создает один тип записи в рабочей области Log Analytics.  

### <a name="heartbeat-records"></a>Записи пульсов
Создается запись с типом **Пульс**.  У этих записей есть свойства, приведенные в таблице ниже.  

| Свойство | ОПИСАНИЕ |
| --- | --- |
| type | *Пульс*|
| Категория | Значение — *Прямой агент*, *Агент SCOM*, или *Сервер управления SCOM*.|
| Компьютер | Имя компьютера.|
| OSType | Операционная система Windows или Linux.|
| OSMajorVersion | Основная версия операционной системы.|
| OSMinorVersion | Второстепенная версия операционной системы.|
| Version (версия) | Версия агента Log Analytics или агента Operations Manager.|
| SCAgentChannel | Значение — *Прямой* и (или) *SCManagementServer*.|
| IsGatewayInstalled | Если шлюз Log Analytics установлен, этот параметр имеет значение *true*, в противном случае — значение *false*.|
| ComputerIP | IP-адрес компьютера.|
| RemoteIPCountry | Географическое расположение, в котором развернут компьютер.|
| ManagementGroupName | Имя группы управления Operations Manager.|
| SourceComputerId | Уникальный идентификатор компьютера.|
| RemoteIPLongitude | Долгота географического расположения компьютера.|
| RemoteIPLatitude | Широта географического расположения компьютера.|

Каждый агент, отправляющий отчеты на сервер управления Operations Manager, будет отправлять два пакета пульса, а значение свойства SCAgentChannel будет включать **Прямой** и **SCManagementServer** в зависимости от того, какие источники данных и решения Log Analytics включены в вашей подписке. Если вы помните, данные из этих решений отправляются непосредственно с сервера управления Operations Manager в Log Analytics или непосредственно с агента в Log Analytics, что зависит от объема данных, собранных с агента. Для событий пульса со значением **SCManagementServer** значением ComputerIP является IP-адрес сервера управления, так как он фактически передает данные.  Для пульсов со значением **Прямой** для параметра SCAgentChannel ComputerIP — это общедоступный IP-адрес агента.  

## <a name="sample-log-searches"></a>Пример поисков журналов
Следующая таблица содержит примеры поисков по журналу для получения записей, собранных этим решением.

| Запрос | ОПИСАНИЕ |
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




## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о генерации оповещений из Log Analytics см. в статье [Оповещения в Log Analytics](../log-analytics/log-analytics-alerts.md).
