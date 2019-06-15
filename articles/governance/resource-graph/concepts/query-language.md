---
title: Основные сведения о языке запросов
description: Описывает доступные операторы Kusto и функции, можно использовать с график ресурсов Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: dcb21a6aedf16b034fad4f0822e22758dda03c33
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65800504"
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

Некоторые имена свойств, например те, которые включают `.` или `$`, должны изолированы или escape-последовательность в запрос или свойства имя интерпретируется неправильно и не дает ожидаемых результатов.

- `.` -Wrap имя свойства таким образом: `['propertyname.withaperiod']`
  
  Пример запроса, который создает оболочку для свойства _odata.type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` -Escape-символ в имени свойства. Escape-символ, используемый зависит от выполнения график ресурсов из оболочки.

  - **Bash** - `\`

    Пример запроса, который не соответствует свойство  _\$тип_ в bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** -не `$` символ.

  - **PowerShell** - ``` ` ```

    Пример запроса, который не соответствует свойство  _\$тип_ в PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Дальнейшие действия

- См. описание используемого языка в разделе [Запросы для начинающих](../samples/starter.md)
- См. описание расширенных вариантов использования в разделе [Расширенные запросы](../samples/advanced.md)
- Подробнее о [просмотре ресурсов](explore-resources.md)