---
title: Azure Cosmos DB совместимость Gremlin с функциями TinkerPop
description: Справочная документация проблемы совместимости модуля Graph
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 581bc813ca27067b1f27ab9866a45df3084dbbcc
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644738"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB совместимость Gremlin
Azure Cosmos DB подсистема графов выглядит в соответствии со спецификацией этапов обхода [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) , но существуют различия.

## <a name="behavior-differences"></a>Различия в поведении

* Модуль Azure Cosmos DB Graph выполняет обход по ***ширине в ширину*** , а TinkerPop Gremlin — первый. Такое поведение обеспечивает лучшую производительность в горизонтально масштабируемых системах, таких как Cosmos DB. 

## <a name="unsupported-features"></a>Неподдерживаемые функции

* ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)***  — это независимая от языка спецификация для обходов графа. Граф Cosmos DB еще не поддерживается. Используйте `GremlinClient.SubmitAsync()` и передайте обход в виде текстовой строки.

* ***`property(set, 'xyz', 1)`*** задать кратность сейчас не поддерживается. Используйте вместо этого `property(list, 'xyz', 1)`. Дополнительные сведения см. в разделе [Свойства вершин с помощью TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* ***`atch()`*** позволяет выполнять запросы к графам с помощью декларативного сопоставления шаблонов. Эта возможность недоступна.

* ***Объекты, так как свойства*** на вершинах или краях не поддерживаются. Как свойства могут использоваться только примитивные типы или массивы.

* ***Сортировка по свойствам массива*** `order().by(<array property>)` не поддерживается. Сортировка поддерживается только для примитивных типов.

* ***Типы JSON, отличные от примитивов*** , не поддерживаются. Используйте типы `false` `string`, `number`или `true`/. `null` значения не поддерживаются. 

* В настоящее время сериализатор ***GraphSONv3*** не поддерживается. Используйте классы `GraphSONv2` сериализаторов, модулей чтения и записи в конфигурации подключения.

* **Лямбда-выражения и функции** в настоящее время не поддерживаются. К ним относятся `.map{<expression>}`, `.by{<expression>}`и функции `.filter{<expression>}`. Дополнительные сведения и о том, как переписать их с помощью Gremlin шагов, см. в [примечании к лямбда-выражениям](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* ***Транзакции*** не поддерживаются из-за распределенного характера системы.  Настройте соответствующую модель согласованности для учетной записи Gremlin, чтобы она читала свои собственные записи, и используйте оптимистичный параллелизм для разрешения конфликтующих операций записи.

## <a name="next-steps"></a>Дальнейшие действия
* Посетите страницу [Cosmos DB пользователя](https://feedback.azure.com/forums/263030-azure-cosmos-db) , чтобы поделиться отзывами и помочь команде сосредоточиться на важных для вас функциях.
