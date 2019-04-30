---
title: Запросы к журналу Azure Monitor | Документация Майкрософт
description: Ссылки на ресурсы для обучения написанию запросов к журналу в Azure Monitor.
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
ms.date: 01/11/2019
ms.author: bwren
ms.openlocfilehash: ecbea91cef69c80975dd105c35f526b357033a6b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61425968"
---
# <a name="azure-monitor-log-queries"></a>Запросы к журналу в Azure Monitor
Журналы Azure Monitor построены на базе Azure Data Explorer, поэтому для работы с журналом Azure Monitor используется такой же язык запросов Kusto. См. дополнительные сведения обо всех возможностях языка в [документации по языку запросов Kusto](/azure/kusto/query) — основном источнике информации по созданию запросов к журналам в Azure Monitor. Эта страница содержит ссылки на другие ресурсы с информацией о создании запросов и описание отличий в разных вариантах реализации языка запросов для Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Приступая к работе

- [Начало работы с Azure Monitor Log Analytics](get-started-portal.md) — это занятие для написания запросов и работа с результатами на портале Azure.
- Статья о [начале работы с запросами к журналам в Azure Monitor](get-started-queries.md) — это руководство по созданию запросов с использованием данных журнала Azure Monitor.

## <a name="concepts"></a>Основные понятия
- В статье об [анализе данных журналов в Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) кратко описаны запросы к журналам и структура данных в журналах Azure Monitor.
- В статье о [просмотре и анализе данных журналов в Azure Monitor](../../azure-monitor/log-query/portals.md) описаны порталы, на которых создаются и выполняются запросы к журналам.

## <a name="reference"></a>Справочные материалы

- [Справочник по языку запросов](/azure/kusto/query) содержит подробную и полную информацию о языке запросов Kusto.
- В статье о [различиях в языках запросов к журналам Azure Monitor](data-explorer-difference.md) сравниваются разные версии языка запросов Kusto.
- В статье о [стандартных свойствах в записях журналов Azure Monitor](../../azure-monitor/platform/log-standard-properties.md) описаны стандартные свойства для всех данных журналов Azure Monitor.
- В статье о [выполнении запросов к журналам нескольких ресурсов в Azure Monitor](../../azure-monitor/log-query/cross-workspace-query.md) описано создание запросов с использованием данных из нескольких рабочих областей Log Analytics и приложений Application Insights.


## <a name="examples"></a>Примеры

- В статье с [примерами запросов к журналам Azure Monitor](examples.md) описаны запросы с использованием данных журналов Azure Monitor.



## <a name="lessons"></a>Уроки

- В статье о [работе со строками в запросах к журналам Azure Monitor](string-operations.md) описано использование строковых данных.
- В статье о [работе со значениями даты и времени в запросах к журналам Azure Monitor](datetime-operations.md) описано использование данных в формате даты и времени. 
- В статьях об [агрегировании в запросах к журналам Azure Monitor](aggregations.md) и [расширенных статистических функциях в запросах к журналам Azure Monitor](advanced-aggregations.md) описаны статистические вычисления и анализ данных.
- В статье об [объединениях в запросах к журналам Azure Monitor](joins.md) описано объединение данных из нескольких таблиц.
- В статье о [работе с JSON и структурой данных в запросах к журналам Azure Monitor](json-data-structures.md) описан анализ данных в формате JSON.
- В статье о [составлении расширенных запросов к журналам в Azure Monitor](advanced-query-writing.md) описана стратегия создания сложных запросов и повторного использования кода.
- В статье о [создании графиков и диаграмм по результатам запросов к журналам Azure Monitor](charts.md) описана визуализации данных, полученных в результате запроса к журналу.

## <a name="cheatsheets"></a>Памятки

-  Статья об [SQL-запросах к журналам Azure Monitor](sql-cheatsheet.md) предназначена для пользователей, которые уже знакомы с SQL.
-  Статья о [Splunk-запросах к журналу Azure Monitor](sql-cheatsheet.md) предназначена для пользователей, которые уже знакомы со Splunk.
 
## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь с полной [справочной документацией по языку запросов Kusto](/azure/kusto/query/).