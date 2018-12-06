---
title: Анализ использования данных в Log Analytics | Документация Майкрософт
description: С помощью панели мониторинга "Использование и ожидаемые затраты" в службе Log Analytics можно оценить, какой объем данных отправляется в Log Analytics, и определить причину непредвиденного увеличения этого объема.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/11/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: e702e1f5eb1816b007317765e4c9a9f88bb99bfd
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52635430"
---
# <a name="analyze-data-usage-in-log-analytics"></a>Анализ использования данных в службе Log Analytics

> [!NOTE]
> В этой статье описывается анализ использования данных в Log Analytics.  Дополнительные сведения приведены в статьях ниже.
> - В статье [Управление затратами на хранение путем регулирования объема и срока хранения данных в Log Analytics](log-analytics-manage-cost-storage.md) описано, как контролировать затраты, изменяя срок хранения данных.
> - В статье [Мониторинг использования и ожидаемых затрат](../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) описано, как можно просмотреть сведения об использовании и оценить затраты по нескольким функциям мониторинга Azure для разных моделей ценообразования. Там также описано, как можно изменить модель ценообразования.

## <a name="understand-usage"></a>Общие сведения о потреблении

Просматривать и анализировать использование данных можно в разделе **Использование и ожидаемые затраты** для Log Analytics. Здесь вы узнаете, какой объем данных был собран каждым решением, какой объем данных сохраняется, и получите оценку затрат на основании объема полученных данных и дополнительных объемов хранения сверх включенных в тариф.

![Использование и ожидаемые затраты](media/log-analytics-usage/usage-estimated-cost-dashboard-01.png)<br>

Чтобы подробнее изучить данные, щелкните значок в верхней правой части любой диаграммы на странице **Использование и ожидаемые затраты**. Теперь вы можете доработать этот запрос, чтобы получить дополнительные сведения о потреблении.  

![Представление журналов](media/log-analytics-usage/logs.png)<br>

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Превышенный объем данных: причины и устранение
Превышенное использование вызывается одной (или двумя) причинами:
- отправлен превышенный объем данных в службу Log Analytics;
- больше узлов, чем ожидалось, отправляют данные в Log Analytics или некоторые узлы отправляют больше данных, чем обычно.

Давайте рассмотрим получение подробных сведений в обоих из этих случаев. 

> [!NOTE]
> Некоторые поля с типом данных Usage (Потребление) уже устарели и данные в них не заполняются, хотя они пока сохраняются в схеме. Например, сюда относятся поля **Computer** и ряд данных о приеме данных (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** и **AverageProcessingTimeMs**).

### <a name="data-volume"></a>Объем данных: 
На странице **Использование и ожидаемые затраты** есть диаграмма *Data ingestion per solution* (Прием данных по решениям), которая позволяет отобразить объем отправленных данных в целом и для каждого решения отдельно. Это позволяет выявить некоторые тенденции, например оценить изменения общего объема используемых данных (или по определенному решению). Для создания этой диаграммы применяется такой запрос:

`Usage| where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

Обратите внимание, что предложение "where IsBillable = true" отсеивает выбранные типы данных из определенных решений, для которых не взимается плата за прием данных. 

Вы можете изучить данные еще подробнее, чтобы найти тенденции для определенных типов данных, например для данных из журналов IIS:

`Usage| where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

### <a name="nodes-sending-data"></a>Узлы, отправляющие данные

Чтобы узнать, сколько количество узлов отправили данные за последний месяц, выполните такой запрос:

`Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(ComputerIP) by bin(TimeGenerated, 1d)    
| render timechart`

Чтобы узнать количество полученных событий для каждого компьютера, выполните такой запрос:

`union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last`

Это довольно дорогостоящий запрос, поэтому применяйте его осторожно. Чтобы узнать количество полученных оплачиваемых событий для каждого компьютера, выполните такой запрос: 

`union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last`

Чтобы узнать, какие оплачиваемые типы данных отправляются на конкретный компьютер, выполните такой запрос:

`union withsource = tt *
| where Computer == "*computer name*"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last `

Чтобы получить более подробную информацию об источнике данных по определенному типу данных, воспользуйтесь приведенными ниже примерами запросов.

+ Решение по **безопасности**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ Решение для **управления журналами**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ Тип данных **Perf**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ Тип данных **Event**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ Тип данных **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ Тип данных **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>Советы по снижению объемов данных

Вот несколько рекомендаций, которые помогут снизить объем собираемых журналов.

| Источник превышенного объема данных | Как сократить объем данных |
| -------------------------- | ------------------------- |
| События безопасности            | Выберите [события со стандартным или минимальным уровнем безопасности](https://blogs.technet.microsoft.com/msoms/2016/11/08/filter-the-security-events-the-oms-security-collects/). <br> Измените политику аудита безопасности таким образом, чтобы собирать только необходимые события. В частности проверьте необходимость сбора следующих событий: <br> - [аудит платформы фильтрации](https://technet.microsoft.com/library/dd772749(WS.10).aspx); <br> - [аудит реестра](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10));<br> - [аудит файловой системы](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10));<br> - [аудит объектов ядра](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10));<br> - [аудит работы с дескрипторами](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10));<br> — аудит съемных носителей. |
| Счетчики производительности       | Измените [конфигурацию счетчика производительности](log-analytics-data-sources-performance-counters.md), чтобы <br> уменьшить частоту сбора или <br> сократить число счетчиков производительности. |
| Журналы событий                 | Измените [конфигурацию журнала событий](log-analytics-data-sources-windows-events.md), чтобы <br> сократить число собранных журналов событий или <br> выполнять сбор только необходимых уровней событий. Например, не выполняйте сбор событий уровня *сведений*. |
| syslog                     | Измените [конфигурацию системного журнала](log-analytics-data-sources-syslog.md), чтобы <br> сократить число собранных объектов или <br> выполнять сбор только необходимых уровней событий. Например, не выполняйте сбор событий уровня *сведений* и *отладки*. |
| AzureDiagnostics           | Измените коллекцию журнала ресурсов, чтобы: <br> Уменьшить число ресурсов, отправляющих журналы в Log Analytics. <br> Выполнять сбор только необходимых журналов. |
| Данные решений с компьютеров, которым не требуется решение | Используйте [нацеливание решений](../azure-monitor/insights/solution-targeting.md), чтобы выполнять сбор данных только в нужных группах компьютеров. |

### <a name="getting-node-counts"></a>Получение счетчиков узлов 

Если вы используете ценовую категорию "За узел (OMS)", плата взимается в зависимости от количества используемых узлов и решений. Количество узлов для предложения "Аналитика", для которых выставляются счета, отображается в таблице на странице **Использование и предполагаемые затраты**.  

Чтобы просмотреть количество отдельных узлов безопасности, выполните такой запрос:

`union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count`

Чтобы просмотреть количество отдельных узлов службы автоматизации, выполните такой запрос:

` ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc`

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>Создание оповещения для превышенного объема сбора данных
В этом разделе описывается создание оповещения для следующих случаев:
- объем данных превышает заданный объем;
- объем данных превысит заданный объем.

Служба "Оповещения Azure" поддерживает [оповещения журналов](../monitoring-and-diagnostics/monitor-alerts-unified-log.md), использующие поисковые запросы. 

Следующий запрос содержит результат, когда за сутки собрано более 100 ГБ данных:

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`

Следующий запрос использует простую формулу для прогноза, что в течение 24 часов будет отправлено более 100 ГБ данных: 

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`

Чтобы создать оповещения для различных объемов данных, измените значение "100" в запросах на число ГБ, для которых вы хотите создать оповещения.

Выполните действия, описанные в разделе о [создании оповещений журналов](../monitoring-and-diagnostics/alert-metric.md), чтобы получать уведомления при превышении ожидаемого объема собираемых данных.

При создании оповещения для первого запроса, когда имеется объем данных, превышающий 100 ГБ в сутки, задайте параметрам следующие значения:  

- Для параметра **Определение условия оповещения** укажите свою рабочую область Log Analytics в качестве целевого ресурса.
- Для **критериев оповещения** укажите следующее:
   - для **названия сигнала** выберите значение **Пользовательский поиск по журналам**;
   - **поисковому запросу** — `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`;
   - **логика оповещений** должна быть **основана на** *числе результатов*, а значение **условия** должно быть *больше* **порогового значения** *0*;
   - укажите **период времени** *1440* минут, а для **частоты оповещений** задайте каждые *60* минут, так как данные об использовании обновляются раз в час.
- В разделе **Определение сведений об оповещении** задайте такие значения:
   - **имени** — *объем данных, превышающий 100 ГБ в сутки*;
   - **серьезности** — *предупреждение*;

Укажите существующую или создайте новую [группу действий](../monitoring-and-diagnostics/monitoring-action-groups.md), чтобы получать соответствующее уведомление, когда оповещение журнала соответствует заданным критериям.

При создании оповещения для второго запроса, когда спрогнозировано, что суточный объем данных превысит 100 ГБ, задайте параметрам следующие значения:

- Для параметра **Определение условия оповещения** укажите свою рабочую область Log Analytics в качестве целевого ресурса.
- Для **критериев оповещения** укажите следующее:
   - для **названия сигнала** выберите значение **Пользовательский поиск по журналам**;
   - **поисковому запросу** — `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`;
   - **логика оповещений** должна быть **основана на** *числе результатов*, а значение **условия** должно быть *больше* **порогового значения** *0*;
   - укажите **период времени** *180* минут, а для **частоты оповещений** задайте каждые *60* минут, так как данные об использовании обновляются один раз в час.
- В разделе **Определение сведений об оповещении** задайте такие значения:
   - **имени** — *объем данных, превышающий 100 ГБ в сутки*;
   - **серьезности** — *предупреждение*;

Укажите существующую или создайте новую [группу действий](../monitoring-and-diagnostics/monitoring-action-groups.md), чтобы получать соответствующее уведомление, когда оповещение журнала соответствует заданным критериям.

Когда вы получите оповещение, выполните действия, описанные в следующем разделе, чтобы определить причину использования превышенного объема данных.

## <a name="next-steps"></a>Дополнительная информация
* Ознакомьтесь со статьей [Поиск данных по журналам](log-analytics-queries.md), чтобы узнать, как использовать язык поиска. Вы можете использовать поисковые запросы, чтобы выполнить дополнительный анализ данных об использовании.
* Выполните действия, описанные в разделе о [создании оповещений журналов](../monitoring-and-diagnostics/alert-metric.md), чтобы получать уведомления при выполнении условий поиска.
* Используйте [нацеливание решений](../azure-monitor/insights/solution-targeting.md), чтобы выполнять сбор данных только в нужных группах компьютеров.
* Сведения о настройке эффективной политики сбора событий безопасности см. в статье [Сбор данных в центре безопасности Azure](../security-center/security-center-enable-data-collection.md).
* Измените [конфигурацию счетчика производительности](log-analytics-data-sources-performance-counters.md).
* Сведения об изменении параметров сбора событий см. в статье [Источники данных для журнала событий Windows в Log Analytics](log-analytics-data-sources-windows-events.md).
* Сведения об изменении конфигурации системного журнала см. в статье [Источники данных для журнала событий Windows в Log Analytics](log-analytics-data-sources-syslog.md).
