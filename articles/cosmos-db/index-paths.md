---
title: 'Работа с путями индекса в Azure Cosmos DB '
description: Обзор путей индекса в Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/13/2019
ms.author: mjbrown
ms.openlocfilehash: d0fce763822ded374eab2f70c3f319aba0c89267
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57992834"
---
# <a name="index-paths-in-azure-cosmos-db"></a>Пути индекса в Azure Cosmos DB

С помощью путей индекса в Azure Cosmos DB вы можете выборочно включить определенный путь в индексацию или исключить его. Выбор определенных путей может обеспечить повышение производительности при записи и понижение требований к хранилищу индекса при сценариях, когда шаблоны запросов известны заранее. Пути индекса начинаются с корневого подстановочного оператора (`/`) и обычно заканчиваются подстановочным оператором `?`. Этот шаблон указывает, что существует несколько возможных значений для префикса. Например, для обслуживания запроса `SELECT * FROM Families F WHERE F.familyName = "Andersen"` необходимо включить путь индекса для запроса `/familyName/?` в политике индексирования контейнера.

В путях индекса можно также использовать оператор подстановочного знака `*`для задания алгоритма пути рекурсивно по префиксу. Например, код `/payload/*` можно использовать для исключения из индексации всего, что находится под свойством payload.

## <a name="common-patterns-for-index-paths"></a>Общие шаблоны для путей индекса

Вот распространенные шаблоны задания путей индекса:

| **Путь** | **Описание/вариант использования** |
| ---------- | ------- |
| /          | Путь коллекции по умолчанию. Является рекурсивным и применяется ко всему дереву документа.|
| /prop/?    | Путь индекса, необходимый для обслуживания следующих запросов (с типами диапазон, соответственно): <br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5 <br><br>SELECT FROM collection c ORDER BY c.prop  |
| /prop/*    | Путь индекса для всех путей по заданной метке. Работает со следующими запросами <br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5<br><br>SELECT FROM collection c WHERE c.prop.subprop.nextprop = "value"<br><br>SELECT FROM collection c ORDER BY c.prop |
| /props/[]/?| Путь индекса, необходимый для обслуживания запросов с итерацией и соединения, отправляемых в массивы скалярных выражений, например, ["a", "b", "c"]:<br><br>SELECT tag FROM tag IN collection.props WHERE tag = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5 |
| /props/[]/subprop/? | Путь индекса, необходимый для обслуживания запросов с итерацией и соединения, отправляемых в массивы объектов, например, [{subprop: "a"}, {subprop: "b"}]:<br><br>SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value" |
| /prop/subprop/? | Путь индекса, необходимый для обслуживания запросов (с типами диапазон, соответственно):<br><br>SELECT FROM collection c WHERE c.prop.subprop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5  |

При задании путей пользовательского индекса необходимо выбрать правило индексации по умолчанию для всего элемента, указав специальный путь `/*`.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об индексации в Azure Cosmos DB в следующих статьях:

- [Indexing in Azure Cosmos DB](index-overview.md) (Индексация в Azure Cosmos DB)
- [Indexing policy in Azure Cosmos DB](indexing-policies.md) (Политики индексации в Azure Cosmos DB)
- [Типы индексов в Azure Cosmos DB](index-types.md)
