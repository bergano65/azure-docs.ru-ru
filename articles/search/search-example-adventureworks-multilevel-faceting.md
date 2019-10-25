---
title: Пример. многоуровневые аспекты
titleSuffix: Azure Cognitive Search
description: Узнайте, как создавать фасетные структуры для многоуровневых таксономий, создавая вложенную структуру переходов, которую можно добавить на страницы приложения.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 8672fa0911d1a031205bb3340fa0c03ab9492a28
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792941"
---
# <a name="example-multi-level-facets-in-azure-cognitive-search"></a>Пример. многоуровневые аспекты в Azure Когнитивный поиск

Схемы Когнитивный поиск Azure не поддерживают в явном виде многоуровневые категории таксономий, но их можно приблизительно оценить, управляя содержимым до индексирования, а затем применяя к результатам специальную обработку. 

## <a name="start-with-the-data"></a>Начало работы с данными

Пример, приведенный в этой статье, основан на предыдущем примере, [моделирует базу данных инвентаризации AdventureWorks](search-example-adventureworks-modeling.md), чтобы продемонстрировать многоуровневые аспекты в Azure когнитивный Поиск.

В базе данных AdventureWorks используется простая двухуровневая таксономия со связью "родитель — потомок". Ввиду фиксированной длины таксономии этой структуры для группирования таксономии можно использовать простой SQL-запрос соединения.

```T-SQL
SELECT 
  parent.Name Parent, category.Name Category, category.ProductCategoryId
FROM 
  SalesLT.ProductCategory category
LEFT JOIN
  SalesLT.ProductCategory parent
  ON category.ParentProductCategoryId=parent.ProductCategoryId
```

  ![Результаты запроса](./media/search-example-adventureworks/prod-query-results.png "Результаты запроса")

## <a name="indexing-to-a-collection-field"></a>Индексирование поля Collection

В индексе, содержащем эту структуру, создайте поле **Collection (EDM. String)** в схеме когнитивный Поиск Azure для хранения этих данных, убедившись, что атрибуты поля включают доступные для поиска, фильтруемые, многогранные и доступные для извлечения данные.

Теперь при индексировании содержимого, которое ссылается на определенную категорию таксономии, отправляйте таксономию как массив, содержащий текст с каждого уровня таксономии. Например, для сущности с `ProductCategoryId = 5 (Mountain Bikes)` отправьте поле как `[ "Bikes", "Bikes|Mountain Bikes"]`.

Обратите внимание на включение родительской категории "Bikes" в значение дочерней категории "Mountain Bikes". В каждую подкатегорию следует внедрить полный путь относительно корневого элемента. Знак вертикальной черты является необязательным, но его применение должно быть должны последовательным и он не должен отображаться в исходном тексте. Знак разделения будет использоваться в коде приложения для воссоздания дерева таксономии из результатов аспекта.

## <a name="construct-the-query"></a>Создание запроса

При выдаче запросов включите следующую спецификацию аспекта (где taxonomy — ваше поле таксономии с аспектами): `facet = taxonomy,count:50,sort:value`

Значение count должно быть достаточно большим для возвращения всех возможных значений таксономии. Данные AdventureWorks содержат 41 уникальное значение таксономии, поэтому `count:50` достаточно.

  ![Фильтр с аспектами](./media/search-example-adventureworks/facet-filter.png "Фильтр с аспектами")

## <a name="build-the-structure-in-client-code"></a>Создание структуры в коде клиента

В коде клиентского приложения воссоздайте дерево таксономии, разделив каждое значение аспекта вертикальной чертой.

```javascript
var sum = 0
  , categories = {children:{},length:0}
  , results = getSearchResults();
separator = separator || '|';
field = field || 'taxonomy';
results['@search.facets'][field].forEach(function(d) {
  var node = categories;
  var parts = d.value.split(separator);
  sum += d.count;
  parts.forEach(function(c, i) {
    if (!_(node.children).has(c)) {
      node.children[c] = {};
      node.children[c].count = d.count;
      node.children[c].children = {};
      node.children[c].length = 0;
      node.children[c].filter = parts.slice(0,i+1).join(separator);
      node.length++;
    }
    node = node.children[c];
  });
});
categories.count = sum;
```

Объект **categories** теперь может использоваться для отображения свертываемого дерева таксономии с точными количествами.

  ![Многоуровневый фильтр с аспектами](./media/search-example-adventureworks/multi-level-facet.png "Многоуровневый фильтр с аспектами")

 
Каждая ссылка в дереве должна применять соответствующий фильтр. Пример.

+ **taxonomy/any**`(x:x eq 'Accessories')` возвращает все документы в ветви Accessories.
+ **taxonomy/any**`(x:x eq 'Accessories|Bike Racks')` возвращает только документы из подкатегории Bike Racks в ветви Accessories.

Этот метод может быть расширен для реализации более сложных сценариев, в том числе более глубоких деревьев таксономии и повторяющихся подкатегорий, которые могут находиться в разных родительских категориях (например, `Bike Components|Forks` и `Camping Equipment|Forks`).

> [!TIP]
> Скорость обработки запросов зависит от количества возвращаемых аспектов. Для поддержки очень больших наборов таксономий рассмотрите возможность добавления фасетного поля **Edm.String** для хранения значения таксономии верхнего уровня для каждого документа. Затем примените приведенную выше методику, но только выполняйте запрос по коллекции аспектов (с фильтром по полю корневой таксономии), когда пользователь разворачивает узел верхнего уровня. Или, если полный результат не требуется, просто сократите число аспектов до разумного количества и убедитесь, что записи аспектов сортируются по количеству.

## <a name="see-also"></a>Дополнительные материалы

[Пример. Моделирование базы данных инвентаризации AdventureWorks для Azure Когнитивный поиск](search-example-adventureworks-modeling.md)