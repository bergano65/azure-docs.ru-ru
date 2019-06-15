---
author: brjohnstmsft
ms.service: search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: b4d0bc73e652a1cd6ef59589318b92f63727c7f5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079643"
---
| Тип данных | Компонентов в лямбда-выражения с `any` | Компонентов в лямбда-выражения с `all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Всем, кроме `search.ismatch` и `search.ismatchscoring` | Аналогично |
| `Collection(Edm.String)` | Сравнение с `eq` или `search.in` <br/><br/> Объединение вложенных выражений с `or` | Сравнение с `ne` или `not search.in()` <br/><br/> Объединение вложенных выражений с `and` |
| `Collection(Edm.Boolean)` | Сравнение с `eq` или `ne` | Аналогично |
| `Collection(Edm.GeographyPoint)` | С помощью `geo.distance` с `lt` или `le` <br/><br/> `geo.intersects` <br/><br/> Объединение вложенных выражений с `or` | С помощью `geo.distance` с `gt` или `ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Объединение вложенных выражений с `and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | Сравнения с использованием `eq`, `ne`, `lt`, `gt`, `le`, или `ge` <br/><br/> Объединение с других вложенных выражений с помощью сравнения `or` <br/><br/> Объединение сравнения, за исключением `ne` с других вложенных выражений с помощью `and` <br/><br/> Выражения с помощью комбинации `and` и `or` в [формы дизъюнктивная Normal (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | Сравнения с использованием `eq`, `ne`, `lt`, `gt`, `le`, или `ge` <br/><br/> Объединение с других вложенных выражений с помощью сравнения `and` <br/><br/> Объединение сравнения, за исключением `eq` с других вложенных выражений с помощью `or` <br/><br/> Выражения с помощью комбинации `and` и `or` в [формы соединительным Normal (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
