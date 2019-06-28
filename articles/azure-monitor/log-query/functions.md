---
title: Функции в запросах журнала Azure Monitor | Документация Майкрософт
description: В этой статье объясняется, как использовать функции для вызова запроса из другого запроса журнала в Azure Monitor.
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
ms.date: 11/15/2018
ms.author: bwren
ms.openlocfilehash: 4b3116230a085bfbb9a6139fbada4179d802bf5e
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296066"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Использование функций в запросах журнала Azure Monitor

Чтобы использовать запрос журнала с другим запросом, сохраните его как функцию. Это позволяет упростить сложные запросы, разбивая их на части, и повторно использовать общий код с несколькими запросами.

## <a name="create-a-function"></a>Создание функции

Создание функции с помощью Log Analytics на портале Azure, щелкнув **Сохранить** и затем указав сведения в следующей таблице.

| Параметр | ОПИСАНИЕ |
|:---|:---|
| Name           | Отображаемое имя для запроса в **обозревателе запросов**. |
| Сохранить как        | Функция |
| Псевдоним функции | Короткое имя функции в других запросах. Не может содержать пробелы и должно быть уникальным. |
| Категория       | Категория для организации сохраненных запросов и функций в **обозревателе запросов**. |

> [!NOTE]
> Функция в Azure Monitor не может содержать другую функцию.

> [!NOTE]
> Функции можно сохранять в запросах Azure Monitor, но это пока недоступно для запросов Application Insights.



## <a name="use-a-function"></a>Использование функции
Используйте функцию, включив ее псевдоним в другой запрос. Она может использоваться как любая другая таблица.

## <a name="example"></a>Пример
Приведенный ниже пример запроса возвращает все отсутствующие обновления безопасности, о которых сообщалось в последний день. Сохраните этот запрос как функцию с псевдонимом _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Создайте другой запрос, который ссылается на функцию _security_updates_last_day_ для поиска нужных обновлений безопасности, связанных с SQL.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с дополнительными уроками о написании запросов журнала Azure Monitor.

- [Работа со строками](string-operations.md)
- [Работа со значениями даты и времени](datetime-operations.md)
- [Статистические функции в запросах Log Analytics](aggregations.md)
- [Расширенные статистические функции в запросах Azure Log Analytics](advanced-aggregations.md)
- [Работа с JSON и структурами данных в запросах Log Analytics](json-data-structures.md)
- [Соединения](joins.md)
- [Создание графиков](charts.md)
