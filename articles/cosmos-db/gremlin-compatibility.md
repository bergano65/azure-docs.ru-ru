---
title: Azure Cosmos DB совместимость Gremlin с функциями TinkerPop
description: Справочная документация проблемы совместимости модуля Graph
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: olignat
ms.openlocfilehash: d58d1ea50251c3568161ce008db2672ff0fd6372
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70911089"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB совместимость Gremlin
Azure Cosmos DB подсистема графов выглядит в соответствии со спецификацией этапов обхода [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) , но существуют различия.

## <a name="behavior-differences"></a>Различия в поведении

* Модуль Azure Cosmos DB Graph выполняет обход по ***ширине в ширину*** , а TinkerPop Gremlin — первый. Такое поведение обеспечивает лучшую производительность в горизонтально масштабируемых системах, таких как Cosmos DB. 

## <a name="unsupported-features"></a>Неподдерживаемые функции

* ***[Gremlin Bytecode](http://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)***  — это независимая от языка спецификация для обходов графа. Граф Cosmos DB еще не поддерживается. Используйте ```GremlinClient.SubmitAsync()``` и передайте обход в виде текстовой строки.

* ***```property(set, 'xyz', 1)```*** Задание количества элементов в настоящее время не поддерживается. Взамен рекомендуется использовать ```property(list, 'xyz', 1)```.

* ***```match()```*** разрешает запросы к графам с помощью декларативного сопоставления шаблонов. Эта возможность недоступна.

* ***Объекты, так как свойства*** на вершинах или краях не поддерживаются. Как свойства могут использоваться только примитивные типы или массивы.

* ***Сортировка по свойствам массива*** ```.order().by(<array property>)``` не поддерживается. Сортировка поддерживается только для примитивных типов.

* ***Типы JSON, отличные от примитивов*** , не поддерживаются. Используйте ```string```типы ```number```, или ```true``` . / ```false``` ```null```значения не поддерживаются. 

* Модуль сериализации ***GraphSONv3*** сейчас недоступен.

* ***Транзакции*** не поддерживаются из-за распределенного характера системы.  Настройте соответствующую модель согласованности для учетной записи Gremlin, чтобы она читала свои собственные записи, и используйте оптимистичный параллелизм для разрешения конфликтующих операций записи.

## <a name="next-steps"></a>Следующие шаги
* Посетите страницу [Cosmos DB пользователя](https://feedback.azure.com/forums/263030-azure-cosmos-db) , чтобы поделиться отзывами и помочь команде сосредоточиться на важных для вас функциях.
