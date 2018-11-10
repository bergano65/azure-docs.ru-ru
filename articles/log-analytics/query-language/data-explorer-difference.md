---
title: Справочник по языку в Log Analytics Azure Monitor | Документация Майкрософт
description: Справочная информация для языка запросов Data Explorer, используемого Log Analytics. Дополнительные элементы, относящиеся к Log Analytics, а также элементы, которые не поддерживаются в запросах Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 65f3388074ff7a84c6e0516a64665019d5883ab1
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913178"
---
# <a name="log-analytics-query-language-differences"></a>Различия в языках запросов Log Analytics

Хотя служба [Log Analytics](../log-analytics-queries.md) создана на базе [Azure Data Explorer](/azure//data-explorer) и использует [тот же язык запросов](/azure/kusto/query), в версиях языка есть некоторые отличия. В этой статье описываются элементы, которые различаются между версией языка, используемого для Data Explorer, и версией, используемой для запросов Log Analytics.

## <a name="data-explorer-elements-not-supported-in-log-analytics"></a>Элементы Data Explorer, которые не поддерживаются в Log Analytics
В следующих разделах описываются элементы языка запросов Data Explorer, которые не поддерживаются в Log Analytics.

### <a name="statements-not-supported-in-log-analytics"></a>Инструкции, которые не поддерживаются в Log Analytics:

* [псевдоним](/kusto/query/aliasstatement);
* [параметры запроса](/azure/kusto/query/queryparametersstatement).

### <a name="functions-not-supported-in-log-analytics"></a>Функции, которые не поддерживаются в Log Analytics:

* [cluster()](/azure/kusto/query/clusterfunction);
* [cursor_after()](/azure/kusto/query/cursorafterfunction);
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction);
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent);
* [database()](/azure/kusto/query/databasefunction);
* [current_principal()](/azure/kusto/query/current-principalfunction);
* [extent_id()](/azure/kusto/query/extentidfunction);
* [extent_tags()](/azure/kusto/query/extenttagsfunction).

### <a name="operators-not-supported-in-log-analytics"></a>Операторы, которые не поддерживаются в Log Analytics:

* [соединение между кластерами](/azure/kusto/query/joincrosscluster);
* [оператор externaldata](/azure/kusto/query/externaldata-operator).

### <a name="plugins-not-supported-in-log-analytics"></a>Подключаемые модули, которые не поддерживаются в Log Analytics:

* [модуль sql_request](/azure/kusto/query/sqlrequestplugin).


## <a name="additional-operators-in-log-analytics"></a>Дополнительные операторы в Log Analytics
Следующие операторы поддерживают конкретные функции Log Analytics и недоступны вне Log Analytics.

* [app()](app-expression.md);
* [workspace()](workspace-expression.md).

## <a name="next-steps"></a>Дополнительная информация

- Получите ссылки на различные [ресурсы для написания запросов Log Analytics](query-language.md).
- Ознакомьтесь с полной [справочной документацией по языку запросов Data Explorer](/azure/kusto/query/).
