---
title: Написание эффективных запросов журналов в Azure Monitor | Документация Майкрософт
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
ms.date: 01/17/2019
ms.author: bwren
ms.openlocfilehash: 25d6b582ed4d4e24df3841f4191471296e25abd8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60519370"
---
# <a name="writing-efficient-log-queries-in-azure-monitor"></a>Написание эффективных запросов журналов в Azure Monitor
Эта статья содержит рекомендации для написания эффективных запросов журналов в Azure Monitor. Применяя эти стратегии, вы сможете гарантировать, что ваши запросы будут выполняться быстро и с минимальными усилиями.

## <a name="scope-your-query"></a>Определение области для выполнения запроса
Если ваш запрос обрабатывает больше данных, чем нужно, он будет выполняться долго и может вернуть в результатах слишком много данных, что препятствует эффективному анализу. В исключительных случаях может быть превышено допустимое время выполнения запроса и произойти сбой.

### <a name="specify-your-data-source"></a>Указание источника данных
Первым шагом в написании эффективного запроса является ограничение его области обязательными источниками данных. Рекомендуется всегда указывать конкретную таблицу, а не выполнять поиск по всему тексту (например, `search *`). Чтобы отправить запрос к определенной таблице, запустите запрос с именем таблицы, как показано ниже:

``` Kusto
requests | ...
```

Вы можете использовать [оператор search](/azure/kusto/query/searchoperator), чтобы искать значение в нескольких столбцах в определенных таблицах, как, например, в следующем запросе:

``` Kusto
search in (exceptions) "The server was not found"

search in (exceptions, customEvents) "timeout"
```

Используйте оператор [union](/azure/kusto/query/unionoperator) для запроса нескольких таблиц, как показано ниже:

``` Kusto
union requests, traces | ...
```

### <a name="specify-a-time-range"></a>Указание диапазона времени
Вам также следует ограничить свой запрос по диапазону времени запрашиваемых данных. По умолчанию запрос будет включать данные, собранные за последние 24 часа. Вы можете изменить этот вариант в [селекторе диапазона времени](get-started-portal.md#select-a-time-range) или явно добавить его в запрос. Лучше всего добавить фильтр времени сразу после имени таблицы, чтобы остальная часть запроса обрабатывала только данные, находящиеся в пределах диапазона:

``` Kusto
requests | where timestamp > ago(1h)

requests | where timestamp between (ago(1h) .. ago(30m))
```
   
### <a name="get-only-the-latest-records"></a>Получение только последних записей

Чтобы вернуть только последние записи, используйте оператор *top* (как в следующем запросе), который возвращает последние 10 записей, зарегистрированных в таблице *traces*:

``` Kusto
traces | top 10 by timestamp
```

   
### <a name="filter-records"></a>Фильтрация записей
Чтобы просмотреть только те журналы, которые соответствуют заданному условию, воспользуйтесь оператором *where* (как в следующем запросе), который возвращает только записи со значением _severityLevel_ больше 0:

``` Kusto
traces | where severityLevel > 0
```



## <a name="string-comparisons"></a>Сравнение строк
При [оценке строк](/azure/kusto/query/datatypes-string-operators) во время поиска полных маркеров обычно следует использовать `has` вместо `contains`. `has` более эффективен, так как не ищет подстроки.

## <a name="returned-columns"></a>Возвращенные столбцы

Используйте оператор [project](/azure/kusto/query/projectoperator), чтобы ограничить обработку только нужным набором столбцов:

``` Kusto
traces 
| project timestamp, severityLevel, client_City 
| ...
```

Хотя вы можете использовать оператор [extend](/azure/kusto/query/extendoperator) для вычисления значений и создания собственных столбцов, обычно эффективнее выполнить фильтрацию по столбцу таблицы.

Например, первый приведенный ниже запрос, который фильтрует по _operation\_Name_, будет более эффективен, чем второй, который создает новый столбец _subscription_ и фильтры для него:

``` Kusto
customEvents 
| where split(operation_Name, "/")[2] == "acb"

customEvents 
| extend subscription = split(operation_Name, "/")[2] 
| where subscription == "acb"
```

## <a name="using-joins"></a>Работа с соединениями
При использовании оператора [join](/azure/kusto/query/joinoperator) укажите таблицу с меньшим числом строк в левой части запроса.


## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения см. в статье [с рекомендациями по выполнению запросов](/azure/kusto/query/best-practices).