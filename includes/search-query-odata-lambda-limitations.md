---
author: brjohnstmsft
ms.service: cognitive-search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: 998d0f1a84dc9cb2a07fb55286c1089787a263e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80272627"
---
| Тип данных | Особенности, разрешенные в выражениях лямбды с`any` | Особенности, разрешенные в выражениях лямбды с`all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Все, `search.ismatch` кроме и`search.ismatchscoring` | Аналогично |
| `Collection(Edm.String)` | Сравнение `eq` с или`search.in` <br/><br/> Объединение субвыражений с`or` | Сравнение `ne` с или`not search.in()` <br/><br/> Объединение субвыражений с`and` |
| `Collection(Edm.Boolean)` | Сравнение `eq` с или`ne` | Аналогично |
| `Collection(Edm.GeographyPoint)` | Использование `geo.distance` `lt` с или`le` <br/><br/> `geo.intersects` <br/><br/> Объединение субвыражений с`or` | Использование `geo.distance` `gt` с или`ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Объединение субвыражений с`and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | Сравнения с `eq` `ne`использованием, , `lt`, `gt`, `le``ge` <br/><br/> Сочетание сравнений с другими подвыражениями с использованием`or` <br/><br/> Сочетание сравнений, за исключением `ne` других подвыражений с использованием`and` <br/><br/> Выражения с использованием `and` `or` комбинаций и в [disjunctive Нормальной форме (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | Сравнения с `eq` `ne`использованием, , `lt`, `gt`, `le``ge` <br/><br/> Сочетание сравнений с другими подвыражениями с использованием`and` <br/><br/> Сочетание сравнений, за исключением `eq` других подвыражений с использованием`or` <br/><br/> Выражения с использованием `and` `or` комбинаций и в [конъюнктурной нормальной форме (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
