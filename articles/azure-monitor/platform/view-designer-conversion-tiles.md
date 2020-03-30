---
title: Дизайнер представления Azure Monitor для конверсий плитки в трудовых книжках
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: f07d15521c787dfd588c285bff57616059caa2f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658632"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Преобразование дизайнерской плитки Azure Monitor
[Дизайнер представления](view-designer.md) — это функция Azure Monitor, которая позволяет создавать пользовательские представления, чтобы помочь вам визуализировать данные в рабочем пространстве Log Analytics с диаграммами, списками и сроками. Они постепенно сворачиваются и заменяются трудовыми книжеками, которые обеспечивают дополнительную функциональность. В этой статье приведены подробные сведения для преобразования различных плиток в трудовые книжки.

## <a name="donut--list-tile"></a>Плитка списка & дона

![Список пончиков](media/view-designer-conversion-tiles/donut-list.png)

Воссоздание плитки списка & пончика в трудовых книжках включает в себя две отдельные визуализации. Для пончик аксута есть два варианта.
Для обоих начать с выбора **Добавить запрос** и вставить исходный запрос из представления дизайнера в ячейку.

**Вариант 1:** Выберите **pie Chart** из меню ![ **визуализации:** меню визуализации диаграммы пирога](media/view-designer-conversion-tiles/pie-chart.png)

**Вариант 2:** Выберите **Набор запроса** из отбрасываемого **визуализации** и добавьте `| render piechart` в запрос:

 ![Меню визуализации](media/view-designer-conversion-tiles/set-by-query.png)

**Пример**

Исходный запрос
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc
```

Обновленный запрос
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc 
| render piechart
```

Для создания списка и включения искры, [common tasks](view-designer-conversion-tasks.md)см.

Ниже приведен пример того, как плитка списка & пончика может быть переосмыслена в трудовых книжках:

![Пончик список трудовых книжек](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>Плитка & список строк
![Список диаграмм строк](media/view-designer-conversion-tiles/line-list.png) 

Чтобы воссоздать часть диаграммы строки, обновите запрос следующим образом:

Исходный запрос
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Обновленный запрос
```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type
```

Существует два варианта визуализации линейной диаграммы

**Вариант 1:** Выберите **диаграмму строки** из сброса **визуализации:**
 
 ![Меню диаграммы строки](media/view-designer-conversion-tiles/line-visualization.png)

**Вариант 2:** Выберите **Набор запроса** из отбрасываемого **визуализации** и добавьте `| render linechart` в запрос:

 ![Меню визуализации](media/view-designer-conversion-tiles/set-by-query.png)

**Пример**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

Для создания списка и включения искры, [common tasks](view-designer-conversion-tasks.md)см.

Ниже приводится пример того, как строка диаграммы & список плитки могут быть переосмыслены в трудовых книжках:

![Список рабочих книг диаграммстрок](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>Плитка списка & номер

 ![Список плитки](media/view-designer-conversion-tiles/tile-list-example.png)

Для текучих чиней обновите запрос следующим образом:

Исходный запрос
```KQL
search * 
| summarize AggregatedValue = count() by Computer | count
```

Обновленный запрос
```KQL
search *
| summarize AggregatedValue = count() by Computer 
| summarize Count = count()
```

Измените выпадение визуализации на **плитки,** а затем выберите **настройки плитки.**
 ![Визуализация плитки](media/view-designer-conversion-tiles/tile-visualization.png)

Оставьте раздел **Раздел заголовка** пустым и выберите **левый**. Измените значение для **столбца Использования:** **для подсчета,** и **отобрасыватель столбцов** к **большому числу:**

![Настройки плитки](media/view-designer-conversion-tiles/tile-settings.png)

 
Для создания списка и включения искры, [common tasks](view-designer-conversion-tasks.md)см.

Ниже приводится пример того, как число & плитки списка могут быть переосмыслены в трудовых книжках:

![Справочники по списку номеров](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>Временная шкала и список

 ![Список временных шкал](media/view-designer-conversion-tiles/time-list.png)

Для временной шкалы обновите запрос следующим образом:

Исходный запрос
```KQL
search * 
| sort by TimeGenerated desc
```

Обновленный запрос
```KQL
search * 
| summarize Count = count() by Computer, bin(TimeGenerated,{TimeRange:grain})
```

Существует два варианта визуализации запроса в виде диаграммы бара:

**Вариант 1:** Выберите **диаграмму бара** из ![выпадения **визуализации:** визуализация Барчарта](media/view-designer-conversion-tiles/bar-visualization.png)
 
**Вариант 2:** Выберите **Набор запроса** из отбрасываемого **визуализации** и добавьте `| render barchart` в запрос:

 ![Меню визуализации](media/view-designer-conversion-tiles/set-by-query.png)

 
Для создания списка и включения искры, [common tasks](view-designer-conversion-tasks.md)см.

Ниже приводится пример того, как плитка & списка сроков может быть переосмыслена в трудовых книжках:

![Справочник хронологии](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Обзор представления дизайнера к переходу к трудовым книгам](view-designer-conversion-overview.md)
