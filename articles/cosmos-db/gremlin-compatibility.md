---
title: Azure Cosmos DB совместимость Gremlin с функциями TinkerPop
description: Справочная документация проблемы совместимости модуля Graph
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 989a033a843b861c34dc9dbdbced50399f8e5cd7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81449890"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB совместимость Gremlin
Azure Cosmos DB подсистема графов выглядит в соответствии со спецификацией этапов обхода [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) , но существуют различия.

## <a name="behavior-differences"></a>Различия в поведении

* Модуль Azure Cosmos DB Graph выполняет обход по ***ширине в ширину*** , а TinkerPop Gremlin — первый. Такое поведение обеспечивает лучшую производительность в горизонтально масштабируемых системах, таких как Cosmos DB. 

## <a name="unsupported-features"></a>Неподдерживаемые функции

* ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)***  — это независимая от языка спецификация для обходов графа. Граф Cosmos DB еще не поддерживается. Используйте `GremlinClient.SubmitAsync()` и передайте обход в виде текстовой строки.

* ***`property(set, 'xyz', 1)`*** Задание количества элементов в настоящее время не поддерживается. Используйте вместо этого `property(list, 'xyz', 1)`. Дополнительные сведения см. в разделе [Свойства вершин с помощью TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* ***`atch()`*** разрешает запросы к графам с помощью декларативного сопоставления шаблонов. Эта возможность недоступна.

* ***Объекты, так как свойства*** на вершинах или краях не поддерживаются. Как свойства могут использоваться только примитивные типы или массивы.

* ***Сортировка по свойствам*** `order().by(<array property>)` массива не поддерживается. Сортировка поддерживается только для примитивных типов.

* ***Типы JSON, отличные от примитивов*** , не поддерживаются. Используйте `string`типы `number`, или `true` / `false` . `null`значения не поддерживаются. 

* В настоящее время сериализатор ***GraphSONv3*** не поддерживается. Используйте `GraphSONv2` классы сериализаторов, модулей чтения и записи в конфигурации подключения. Результаты, возвращаемые API Azure Cosmos DB Gremlin, имеют формат GraphSON. 

* **Лямбда-выражения и функции** в настоящее время не поддерживаются. К ним относятся `.map{<expression>}` `.filter{<expression>}` функции, `.by{<expression>}`и. Дополнительные сведения и о том, как переписать их с помощью Gremlin шагов, см. в [примечании к лямбда-выражениям](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* ***Транзакции*** не поддерживаются из-за распределенного характера системы.  Настройте соответствующую модель согласованности для учетной записи Gremlin, чтобы она читала свои собственные записи, и используйте оптимистичный параллелизм для разрешения конфликтующих операций записи.

## <a name="next-steps"></a>Дальнейшие шаги
* Посетите страницу [Cosmos DB пользователя](https://feedback.azure.com/forums/263030-azure-cosmos-db) , чтобы поделиться отзывами и помочь команде сосредоточиться на важных для вас функциях.
