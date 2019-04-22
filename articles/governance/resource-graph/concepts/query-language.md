---
title: Основные сведения о языке запросов
description: Описывает доступные операторы Kusto и функции, можно использовать с график ресурсов Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/11/2018
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 08e4f09665a3501073f55b7f5b82bf51cf508ea9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59276683"
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

## <a name="next-steps"></a>Дальнейшие действия

- См. описание используемого языка в разделе [Запросы для начинающих](../samples/starter.md)
- См. описание расширенных вариантов использования в разделе [Расширенные запросы](../samples/advanced.md)
- Подробнее о [просмотре ресурсов](explore-resources.md)