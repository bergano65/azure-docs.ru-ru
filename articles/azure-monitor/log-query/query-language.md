---
title: Язык запросов Log Analytics в Azure Monitor | Документация Майкрософт
description: Ссылки на ресурсы для обучения созданию запросов в Log Analytics.
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
ms.date: 10/29/2018
ms.author: bwren
ms.openlocfilehash: 32e64ce7772d562ea34a0d74afbd737be27d247d
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968874"
---
# <a name="log-analytics-query-language"></a>Язык запросов Log Analytics
Log Analytics выполняет для Azure Monitor анализ и сбор журналов. Эта служба создана на основе обозревателя данных Azure и использует такую же версию языка запросов. В [документации по языку запросов обозревателя данных Azure](/azure/kusto/query) предоставлены подробные сведения обо всех возможностях языка. Ее следует считать основным источником информации о создании запросов в Log Analytics. Эта страница содержит ссылки на другие ресурсы с информацией о том, как создавать запросы и чем отличается реализация языка запросов для Log Analytics.

## <a name="getting-started"></a>Приступая к работе

- [Начало работы с Log Analytics на портале Azure](get-started-portal.md) — это урок по созданию запросов и анализу результатов на портале Azure.
-  [Начало работы с запросами в Log Analytics](get-started-queries.md) — это урок по написанию запросов на основе данных Log Analytics.

## <a name="concepts"></a>Основные понятия
- [Анализ данных Log Analytics в Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) — краткий обзор запросов по журналам и описание структуры данных Log Analytics.
- [Просмотр и анализ данных в Log Analytics](../../azure-monitor/log-query/portals.md) — описание порталов, на которых создаются и выполняются запросы Log Analytics.

## <a name="reference"></a>Справочные материалы

- [Справочник по языку запросов](/azure/kusto/query) содержит подробную и полную информацию о языке запросов обозревателя данных.
- [Log Analytics query language differences](data-explorer-difference.md) (Отличия в языке запросов Log Analytics) — описание различий между версиями языка запросов обозревателя данных.
- [Стандартные свойства в записях Log Analytics](../../azure-monitor/platform/log-standard-properties.md) — описание свойств, которые являются стандартными для всех данных в Log Analytics.
- [Выполнение поиска по журналам нескольких ресурсов в Log Analytics](../../azure-monitor/log-query/cross-workspace-query.md) — описание того, как создавать запросы на основе данных из нескольких рабочих областей Log Analytics и приложений Application Insights.


## <a name="examples"></a>Примеры

- [Примеры запросов Log Analytics](examples.md) — примеры запросов по данным Log Analytics.



## <a name="lessons"></a>Занятия

- [Работа со строками в запросах Log Analytics](string-operations.md) — описание работы со строковыми данными.
- [Работа со значениями даты и времени в запросах Log Analytics](datetime-operations.md) — описание работы с данными в формате даты и времени. 
- [Агрегирование в запросах Log Analytics](aggregations.md) и [Расширенные статистические функции в запросах Log Analytics](advanced-aggregations.md) — описание процессов статистической обработки и анализа данных.
- [Объединения в запросах Log Analytics](joins.md) — объясняется, как объединить данные из нескольких таблиц.
- [Работа с JSON и структурой данных в запросах Log Analytics](json-data-structures.md) — описание синтаксического анализа данных в формате JSON.
- [Составление расширенных запросов в Log Analytics](advanced-query-writing.md) — описание стратегии создания сложных запросов и повторного использования кода.
- [Создание графиков и диаграмм из запросов Log Analytics](charts.md) — описание визуализации данных, полученных из запроса.

## <a name="cheatsheets"></a>Памятки

-  [Таблица соответствия SQL и языка запросов Log Analytics](sql-cheatsheet.md) поможет тем пользователям, которые уже знакомы с SQL.
-  [Сравнение Splunk с Log Analytics](sql-cheatsheet.md) поможет тем пользователям, которые уже знакомы с Splunk.
 
## <a name="next-steps"></a>Дополнительная информация

- Ознакомьтесь с полной [справочной документацией по языку запросов обозревателя данных](/azure/kusto/query/).