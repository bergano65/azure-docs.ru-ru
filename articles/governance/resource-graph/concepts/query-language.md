---
title: Основные сведения о языке запросов
description: Описание доступных операторов и функций Kusto, которые можно использовать с графом ресурсов Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: c6e35d688581d0839e12806117e63c7d71fbc459
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231514"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Общие сведения о языке запросов графика ресурсов Azure

Язык запросов для графика ресурсов Azure поддерживает ряд операторов и функций. Все из них работают на основе [обозревателя данных Azure](../../../data-explorer/data-explorer-overview.md).

Для наиболее эффективного ознакомления с языком запросов, используемым графиком ресурсов, рекомендуется начать с документации по [языку запросов](/azure/kusto/query/index) обозревателя данных Azure. Это дает представление о структуре языка и о том, каким образом различные поддерживаемые операторы и функции работают вместе.

## <a name="supported-tabular-operators"></a>Поддерживаемые табличные операторы

Ниже приведен список поддерживаемых табличных операторов в графике ресурсов:

- [count](/azure/kusto/query/countoperator)
- [distinct](/azure/kusto/query/distinctoperator)
- [extend](/azure/kusto/query/extendoperator)
- [limit](/azure/kusto/query/limitoperator)
- [order by](/azure/kusto/query/orderoperator)
- [project](/azure/kusto/query/projectoperator)
- [project-away](/azure/kusto/query/projectawayoperator)
- [sample](/azure/kusto/query/sampleoperator)
- [sample-distinct](/azure/kusto/query/sampledistinctoperator)
- [sort by](/azure/kusto/query/sortoperator)
- [summarize](/azure/kusto/query/summarizeoperator)
- [take](/azure/kusto/query/takeoperator)
- [В начало](/azure/kusto/query/topoperator)
- [top-nested](/azure/kusto/query/topnestedoperator)
- [top-hitters](/azure/kusto/query/tophittersoperator)
- [where](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>Поддерживаемые функции

Ниже приведен список поддерживаемых функций в графике ресурсов:

- [ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [tostring()](/azure/kusto/query/tostringfunction)
- [zip()](/azure/kusto/query/zipfunction)

## <a name="escape-characters"></a>Escape-символы

Некоторые имена свойств, например те, которые включают `.` или `$`, должны быть заключены в запрос или экранированы в запросе, либо имя свойства интерпретируется неправильно и не предоставляет ожидаемые результаты.

- `.`— Заключите имя свойства в следующее:`['propertyname.withaperiod']`
  
  Пример запроса, который заключает в оболочку свойство _OData. Type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`— Escape-последовательность символа в имени свойства. Используемый escape-символ зависит от графа ресурсов оболочки, из которого выполняется.

  - **Bug** - `\`

    Пример запроса, который экранирование  _\$типа_ свойства в Bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** — не Escape `$` -символ.

  - **PowerShell** - ``` ` ```

    Пример запроса, который обходит  _\$тип_ свойства в PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Следующие шаги

- См. описание используемого языка в разделе [Запросы для начинающих](../samples/starter.md)
- См. описание расширенных вариантов использования в разделе [Расширенные запросы](../samples/advanced.md)
- Подробнее о [просмотре ресурсов](explore-resources.md)