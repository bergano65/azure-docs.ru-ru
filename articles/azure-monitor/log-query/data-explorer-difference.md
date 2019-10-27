---
title: Различия в языках запросов журнала Azure Monitor | Документация Майкрософт
description: Справочная информация о языке запросов Kusto, используемого в Azure Monitor. Статья включает дополнительные элементы, относящиеся к Azure Monitor, а также элементы, которые не поддерживаются в запросах журнала Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 16a221c0530d65e11589ac272a8e032de0cd3d2b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933097"
---
# <a name="azure-monitor-log-query-language-differences"></a>Различия в языках запросов журнала Azure Monitor

Хотя [журналы в Azure Monitor](log-query-overview.md) созданы на базе [Azure Data Explorer](/azure/data-explorer) и используют [тот же язык запросов Kusto](/azure/kusto/query), в версиях языка есть некоторые отличия. В этой статье описываются элементы, которые различаются между версией языка, используемого для Data Explorer, и версией, используемой для запросов журнала Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>Элементы языка запросов Kusto, которые не поддерживаются в Azure Monitor
В следующих разделах описываются элементы языка запросов Kusto, которые не поддерживаются в Azure Monitor.

### <a name="statements-not-supported-in-azure-monitor"></a>Инструкции, которые не поддерживаются в Azure Monitor:

* [псевдоним](/azure/kusto/query/aliasstatement);
* [параметры запроса](/azure/kusto/query/queryparametersstatement).

### <a name="functions-not-supported-in-azure-monitor"></a>Функции, которые не поддерживаются в Azure Monitor:

* [cluster()](/azure/kusto/query/clusterfunction);
* [cursor_after()](/azure/kusto/query/cursorafterfunction);
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction);
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent);
* [database()](/azure/kusto/query/databasefunction);
* [current_principal()](/azure/kusto/query/current-principalfunction);
* [extent_id()](/azure/kusto/query/extentidfunction);
* [extent_tags()](/azure/kusto/query/extenttagsfunction).

### <a name="operators-not-supported-in-azure-monitor"></a>Операторы, которые не поддерживаются в Azure Monitor:

* [соединение между кластерами](/azure/kusto/query/joincrosscluster);
* [оператор externaldata](/azure/kusto/query/externaldata-operator).

### <a name="plugins-not-supported-in-azure-monitor"></a>Подключаемые модули, которые не поддерживаются в Azure Monitor:

* [Подключаемый модуль Python](/azure/kusto/query/pythonplugin)
* [модуль sql_request](/azure/kusto/query/sqlrequestplugin).


## <a name="additional-operators-in-azure-monitor"></a>Дополнительные операторы в Azure Monitor
Следующие операторы поддерживают конкретные функции Azure Monitor и недоступны вне Azure Monitor.

* [app()](app-expression.md);
* [workspace()](workspace-expression.md).

## <a name="next-steps"></a>Дальнейшие действия

- Получите ссылки на различные [ресурсы для написания запросов журнала Azure Monitor](query-language.md).
- Ознакомьтесь с полной [справочной документацией по языку запросов Kusto](/azure/kusto/query/).
