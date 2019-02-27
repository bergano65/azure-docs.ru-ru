---
title: Создание диаграмм и схем из запросов журнала Azure Monitor | Документация Майкрософт
description: В статье описаны различные визуализации в Azure Monitor для разных способов отображения данных журнала.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 07d0866bd697587da170a00e8077a57035989d32
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56269422"
---
# <a name="creating-charts-and-diagrams-from-azure-monitor-log-queries"></a>Создание диаграмм и схем из запросов журнала Azure Monitor

> [!NOTE]
> Перед тем как приступать к этому уроку, следует изучить статью [Расширенные статистические функции в запросах Log Analytics](advanced-aggregations.md).

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

В этой статье описаны различные визуализации в Azure Monitor для разных способов отображения данных журнала.

## <a name="charting-the-results"></a>Отображение результатов
Начните с изучения того, сколько компьютеров работают в операционной системе за последний час.

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

По умолчанию результаты отображаются в виде таблицы:

![Таблица](media/charts/table-display.png)

Чтобы получить более полное представление, выберите **Диаграмма** и выберите параметр **Круговая**, чтобы отображать результаты:

![Круговая диаграмма](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>Временные диаграммы
Показать среднее количество 50- и 95-процентильной загруженности процессора в ячейках за 1 час. Запрос создает несколько рядов, а затем можно выбрать, какие ряды отображать на диаграмме времени.

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Выберите вариант отображения диаграммы **Линейная**.

![График](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>Справочные данные

Справочные данные помогут легко определить, превышает ли метрика указанный порог. Чтобы добавить строку в диаграмму, расширьте набор данных столбцом константы.

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Справочные данные](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>Несколько измерений
Несколько выражений в значении `by` из `summarize` в результате создают несколько строк, по одному для каждой комбинации значений.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

При просмотре результатов в виде диаграммы используется первый столбец из предложения `by`. В следующем примере показана гистограмма с накоплением с использованием _EventID._ Измерения должны быть типа `string`, поэтому в этом примере _EventID_ приводится к строкам. 

![Гистограмма EventID](media/charts/charts-and-diagrams-multiDimension1.png)

Можно переключаться между ними, выбирая выпадающее меню с именем столбца. 

![Гистограмма AccountType](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь с другими статьями по использованию [языка запросов Kusto](/azure/kusto/query/) с данными журналов Azure Monitor.

- [Работа со строками](string-operations.md)
- [Работа со значениями даты и времени](datetime-operations.md)
- [Статистические функции в запросах Log Analytics](aggregations.md)
- [Расширенные статистические функции в запросах Azure Log Analytics](advanced-aggregations.md)
- [Работа с JSON и структурами данных в запросах Log Analytics](json-data-structures.md)
- [Составление расширенных запросов](advanced-query-writing.md)
- [Соединения](joins.md)