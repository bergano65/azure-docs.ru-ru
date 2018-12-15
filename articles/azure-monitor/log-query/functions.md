---
title: Функции в Azure Log Analytics | Документация Майкрософт
description: В этой статье объясняется, как использовать функции для вызова запроса из другого запроса в Log Analytics.
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
ms.openlocfilehash: 8f2855ed56d298ec4c6abee02dd59ce9471f0d2e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52885327"
---
# <a name="using-functions-in-azure-monitor-log-analytics"></a>Использование функций в Azure Monitor Log Analytics

> [!NOTE]
> Прежде чем приступить к этому уроку, необходимо ознакомиться со статьями [Начало работы с порталом аналитики](get-started-portal.md) и [Начало работы с запросами](get-started-queries.md).

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]


Чтобы использовать запрос Log Analytics с другим запросом, можно сохранить его как функцию. Это позволяет упростить сложные запросы, разбивая их на части, и повторно использовать общий код с несколькими запросами.

## <a name="create-a-function"></a>Создание функции

Создайте функцию на портале Azure. Для этого щелкните **Сохранить** и укажите сведения из приведенной ниже таблицы.

| Параметр | ОПИСАНИЕ |
|:---|:---|
| ИМЯ           | Отображаемое имя для запроса в **обозревателе запросов**. |
| Сохранить как        | Функция |
| Псевдоним функции | Короткое имя функции в других запросах. Не может содержать пробелы и должно быть уникальным. |
| Категория       | Категория для организации сохраненных запросов и функций в **обозревателе запросов**. |

> [!NOTE]
> Функция в Log Analytics не может содержать другую функцию.

> [!NOTE]
> Функции можно сохранять в запросах Log Analytics, но это пока недоступно для запросов Application Insights.



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

## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь с другими статьями по использованию языка запросов Log Analytics:

- [Работа со строками](string-operations.md)
- [Работа со значениями даты и времени](datetime-operations.md)
- [Статистические функции в запросах Log Analytics](aggregations.md)
- [Расширенные статистические функции в запросах Azure Log Analytics](advanced-aggregations.md)
- [Работа с JSON и структурами данных в запросах Log Analytics](json-data-structures.md)
- [Соединения](joins.md)
- [Создание графиков](charts.md)
