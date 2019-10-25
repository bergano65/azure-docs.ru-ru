---
title: Пример. Моделирование базы данных инвентаризации AdventureWorks
titleSuffix: Azure Cognitive Search
description: Узнайте, как моделировать реляционные данные, преобразуя их в плоский набор данных для индексирования и полнотекстового поиска в Когнитивный поиск Azure.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: heidist
ms.openlocfilehash: edb6162724938962df8a7340afea6e930a0b1049
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792993"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-cognitive-search"></a>Пример. Моделирование базы данных инвентаризации AdventureWorks для Azure Когнитивный поиск

Когнитивный поиск Azure принимает плоский набор строк в качестве входных данных для [конвейера индексирования (приема данных)](search-what-is-an-index.md). Если исходные данные исходят из SQL Server реляционной базы данных, в этой статье демонстрируется один из подходов к созданию плоского набора строк до индексирования с использованием образца базы данных AdventureWorks в качестве примера.

## <a name="about-adventureworks"></a>О AdventureWorks

Если у вас есть экземпляр SQL Server, возможно, вы знакомы с [образцом базы данных AdventureWorks](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017). Среди таблиц, содержащихся в этой базе данных, есть пять таблиц, которые предоставляют сведения о продуктах.

+ **ProductModel**: имя.
+ **Product**: имя, цвет, цена, размер, вес, изображение, категория (каждая строка соединена с конкретным элементом ProductModel).
+ **ProductDescription**: описание.
+ **ProductModelProductDescription**: языковой стандарт (каждая строка соединяет ProductModel с определенным элементом ProductDescription для конкретного языка).
+ **ProductCategory**: имя, родительская категория.

Цель этого примера заключается в объединении всех этих данных в плоский набор строк, который может быть принят в индекс поиска. 

## <a name="considering-our-options"></a>Рассмотрение доступных вариантов

Необдуманный подход заключается в индексировании всех строк из таблицы Product (присоединенных, если это уместно), так как именно таблица Product содержит наиболее информативные сведения. Однако такой подход приведет к тому, что индекс поиска будет содержать очевидные дубликаты в результирующем наборе. Например, модель Road-650 доступна в шести размерах и двух цветах. Запрос "road bikes" выдаст двенадцать экземпляров одной и той же модели, различающихся только размером и цветом. Шесть других дорожных моделей окажутся "на задворках" — на второй странице результатов поиска.

  ![Список продуктов](./media/search-example-adventureworks/products-list.png "Список продуктов")
 
Обратите внимание на то, что для модели Road-650 доступны двенадцать вариантов. Строки сущности "один ко многим" лучше всего могут быть представлены как многозначные поля или поля с предварительно объединенными значениями в индексе поиска.

Решить эту задачу простым перемещением целевого индекса в таблицу ProductModel не получится. Это приведет к пропуску важных данных в таблице Product, которые должны быть представлены в результатах поиска.

## <a name="use-a-collection-data-type"></a>Использование типа данных Collection

Правильным подходом является использование функции поиска схемы, которая не имеет прямой параллельной обработки в модели базы данных: **Collection (EDM. String)** . Эта конструкция определена в схеме индекса Когнитивный поиск Azure. Тип данных коллекции используется, если необходимо представить список отдельных строк, а не очень длинную (одну) строку. Если используются ключевые слова или теги, для этого поля лучше использовать тип данных Collection.

Если определить многозначные поля индекса **Collection(Edm.String)** для цвета, размера и изображения, то будут сохранены вспомогательные сведения для применения аспектов и фильтров без засорения индекса повторяющимися записями. Аналогичным образом примените агрегатные функции к числовым полям Product, индексируя **minListPrice** вместо **listPrice** для каждого отдельного продукта.

При индексе с этими структурами поиск фразы "mountain bikes" выдаст разные модели велосипедов, сохраняя такие важные метаданные, как цвет, размер и наименьшая цена. Это показано на следующем снимке экрана.

  ![Пример поиска горных велосипедов](./media/search-example-adventureworks/mountain-bikes-visual.png "Пример поиска горных велосипедов")

## <a name="use-script-for-data-manipulation"></a>Использование сценария для работы с данными

К сожалению, этот тип моделирования невозможно легко реализовать только с помощью инструкций SQL. Вместо этого следует использовать простой сценарий NodeJS для загрузки данных и их сопоставления с сущностями JSON с поддержкой поиска.

Завершающее сопоставление данных для поиска в базе данных выглядит следующим образом:

+ model (Edm.String: searchable, filterable, retrievable) из ProductModel.Name;
+ description_en (Edm.String: searchable) из ProductDescription для модели, где culture="en";
+ color (Collection(Edm.String): searchable, filterable, facetable, retrievable): уникальные значения из Product.Color для модели;
+ size (Collection(Edm.String): searchable, filterable, facetable, retrievable): уникальные значения из Product.Size для модели;
+ image (Collection(Edm.String): retrievable): уникальные значения из Product.ThumbnailPhoto для модели;
+ minStandardCost (Edm.Double: filterable, facetable, sortable, retrievable): объединенное минимальное значение среди всех значений Product.StandardCost для модели;
+ minListPrice (Edm.Double: filterable, facetable, sortable, retrievable): объединенное минимальное значение среди всех значений Product.ListPrice для модели;
+ minWeight (Edm.Double: filterable, facetable, sortable, retrievable): объединенное минимальное значение среди всех значений Product.Weight для модели;
+ products (Collection(Edm.String): searchable, filterable, retrievable): уникальные значения Product.Name для модели.

После соединения таблицы ProductModel с Product и ProductDescription используйте [lodash](https://lodash.com/) (или Linq в C#) для быстрого преобразования результирующего набора.

```javascript
var records = queryYourDatabase();
var models = _(records)
  .groupBy('ModelName')
  .values()
  .map(function(d) {
    return {
      model: _.first(d).ModelName,
      description: _.first(d).Description,
      colors: _(d).pluck('Color').uniq().compact().value(),
      products: _(d).pluck('ProductName').uniq().compact().value(),
      sizes: _(d).pluck('Size').uniq().compact().value(),
      images: _(d).pluck('ThumbnailPhotoFilename').uniq().compact().value(),
      minStandardCost: _(d).pluck('StandardCost').min(),
      maxStandardCost: _(d).pluck('StandardCost').max(),
      minListPrice: _(d).pluck('ListPrice').min(),
      maxListPrice: _(d).pluck('ListPrice').max(),
      minWeight: _(d).pluck('Weight').min(),
      maxWeight: _(d).pluck('Weight').max(),
    };
  })
  .value();
```

Результирующие данные JSON выглядят следующим образом.

```json
[
  {
    "model": "HL Road Frame",
    "colors": [
      "Black",
      "Red"
    ],
    "products": [
      "HL Road Frame - Black, 58",
      "HL Road Frame - Red, 58",
      "HL Road Frame - Red, 62",
      "HL Road Frame - Red, 44",
      "HL Road Frame - Red, 48",
      "HL Road Frame - Red, 52",
      "HL Road Frame - Red, 56",
      "HL Road Frame - Black, 62",
      "HL Road Frame - Black, 44",
      "HL Road Frame - Black, 48",
      "HL Road Frame - Black, 52"
    ],
    "sizes": [
      "58",
      "62",
      "44",
      "48",
      "52",
      "56"
    ],
    "images": [
      "no_image_available_small.gif"
    ],
    "minStandardCost": 868.6342,
    "maxStandardCost": 1059.31,
    "minListPrice": 1431.5,
    "maxListPrice": 1431.5,
    "minWeight": 961.61,
    "maxWeight": 1043.26
  }
]
```

Наконец, ниже приведен SQL-запрос для возвращения начального набора записей. Для загрузки данных в мое приложение NodeJS был использован модуль npm [mssql](https://www.npmjs.com/package/mssql).

```T-SQL
SELECT
  m.Name as ModelName,
  d.Description,
  p.Name as ProductName,
  p.*
FROM 
  SalesLT.ProductModel m
INNER JOIN 
  SalesLT.ProductModelProductDescription md
  ON m.ProductModelId = md.ProductModelId
INNER JOIN 
  SalesLT.ProductDescription d
  ON md.ProductDescriptionId = d.ProductDescriptionId
LEFT JOIN 
  SalesLT.product p
  ON m.ProductModelId = p.ProductModelId
WHERE
  md.Culture='en'
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Пример. таксономии многоуровневого аспекта в Azure Когнитивный поиск](search-example-adventureworks-multilevel-faceting.md)