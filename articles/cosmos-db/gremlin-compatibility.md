---
title: Совместимость Azure Cosmos DB Gremlin с функциями TinkerPop
description: Справочная документация Граф проблемы совместимости двигателя
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 989a033a843b861c34dc9dbdbced50399f8e5cd7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81449890"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Совместимость Azure Cosmos DB Gremlin
Двигатель Azure Cosmos DB Graph внимательно следит за спецификацией шагов обхода [Apache TinkerPop,](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) но есть различия.

## <a name="behavior-differences"></a>Различия в поведении

* Двигатель Azure Cosmos DB Graph работает в ***ширину в первую*** очередь, в то время как TinkerPop Gremlin является первым на глубину. Такое поведение обеспечивает лучшую производительность в горизонтально масштабируемой системе, такой как Cosmos DB. 

## <a name="unsupported-features"></a>Неподдерживаемые функции

* ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)***  — это независимая от языка спецификация для обходов графа. Cosmos DB Graph пока не поддерживает его. Используйте `GremlinClient.SubmitAsync()` и проходите обход в качестве текстовой строки.

* ***`property(set, 'xyz', 1)`*** набор кардинальности не поддерживается сегодня. Используйте вместо этого `property(list, 'xyz', 1)`. Чтобы узнать больше, смотрите [свойства Vertex с TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* ***`atch()`*** позволяет загоните графики с помощью декларативного сопоставления шаблонов. Эта возможность недоступна.

* ***Объекты как свойства*** на вершинах или краях не поддерживаются. Как свойства могут использоваться только примитивные типы или массивы.

* ***Сортировка по свойствам*** `order().by(<array property>)` массива не поддерживается. Сортировка поддерживается только для примитивных типов.

* ***Непримитивные типы JSON*** не поддерживаются. `string`Используйте, `number` `true` / `false` или типы. `null`значения не поддерживаются. 

* В настоящее время серизатор ***GraphSONv3*** не поддерживается. Используйте `GraphSONv2` классы Serializer, Reader и Writer в конфигурации соединения. Результаты, полученные API Azure Cosmos DB Gremlin, не имеют такого же формата, как формат GraphSON. 

* **Выражения и функции Lambda** в настоящее время не поддерживаются. Это включает `.map{<expression>}`в `.by{<expression>}`себя `.filter{<expression>}` , и функции. Чтобы узнать больше, и узнать, как переписать их с помощью шагов Гремлин, [см.](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas)

* ***Транзакции*** не поддерживаются из-за распределенного характера системы.  Настроили соответствующую модель согласованности на счетгремлин, чтобы «читать свои собственные записи» и использовать оптимистический параллел для разрешения противоречивых записей.

## <a name="next-steps"></a>Следующие шаги
* Посетите голосовую страницу [пользователя Cosmos DB,](https://feedback.azure.com/forums/263030-azure-cosmos-db) чтобы поделиться отзывами и помочь команде сосредоточиться на важных для вас функциях.
