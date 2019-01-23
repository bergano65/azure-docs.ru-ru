---
title: Работа со строками в запросах Azure Log Analytics | Документация Майкрософт
description: В этой статье содержатся сведения об использовании портала аналитики для записи запросов в Log Analytics.
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
ms.openlocfilehash: 24f22d659ccfb6923ad2a038e12454716b2c5445
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54263889"
---
# <a name="working-with-json-and-data-structures-in-log-analytics-queries"></a>Работа с JSON и структурой данных в запросах Log Analytics

> [!NOTE]
> Прежде чем приступить к этому уроку, необходимо ознакомиться со статьями [Начало работы с порталом аналитики](get-started-portal.md) и [Начало работы с запросами](get-started-queries.md).

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Вложенные объекты — это объекты, которые содержат другие объекты в виде массива или карты пар "ключ — значение". Эти объекты представлены как строки JSON. В этой статье описывается, как JSON используется для извлечения данных и их анализа вложенных объектов.

## <a name="working-with-json-strings"></a>Работа со строками JSON
Используйте `extractjson`, чтобы получить доступ к определенному элементу JSON с известным путем. Эта функция требует выражение пути, которое использует следующие правила.

- _$_ для обращения к корневой папке
- Использование скобок или точечной нотации для обращения к индексам и элементам, как показано в следующих примерах.


Использование скобок для индексов и точек для разделения элементов:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Тот же пример с использованием только скобочной нотации:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Если имеется только один элемент, можно использовать только точечную нотацию:

```Kusto
let hosts_report='{"location":"North_DC", "status":"running", "rate":5}';
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Работа с объектами

### <a name="parsejson"></a>parsejson
Чтобы получить доступ к нескольким элементам в структуре json, проще открыть его как динамический объект. Используйте `parsejson` для приведения текстовых данных к динамическому объекту. После преобразования в динамический тип для анализа данных могут использоваться дополнительные функции.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
Используйте `arraylength` для подсчета числа элементов в массиве:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Используйте `mvexpand`, чтобы разбить свойства объекта на отдельные строки.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
Используйте `buildschema` для получения схемы, которая учитывает все значения объекта:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

Выходные данные — это схема в формате JSON.
```json
{
    "hosts":
    {
        "indexer":
        {
            "location": "string",
            "rate": "int",
            "status": "string"
        }
    }
}
```
Эти выходные данные описывают имена полей объекта и соответствующие им типы данных. 

Вложенные объекты могут иметь разные схемы, такие как в следующем примере:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Создание схемы](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь с другими статьями по использованию языка запросов Log Analytics:

- [Работа со строками](string-operations.md)
- [Работа со значениями даты и времени](datetime-operations.md)
- [Статистические функции в запросах Log Analytics](aggregations.md)
- [Расширенные статистические функции в запросах Azure Log Analytics](advanced-aggregations.md)
- [Составление расширенных запросов](advanced-query-writing.md)
- [Соединения](joins.md)
- [Создание графиков](charts.md)