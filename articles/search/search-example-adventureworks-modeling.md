---
title: 'Пример: моделирование базы данных инвентаризации AdventureWorks для Поиска Azure'
description: Узнайте, как моделировать реляционные данные, преобразуя их в плоский набор данных, для индексирования и полнотекстового поиска в Поиске Azure.
author: cstone
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: chstone
ms.openlocfilehash: 6d5d01dfbbcfda56818f5c38b06117a87e021445
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61291914"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-search"></a>Пример: моделирование базы данных инвентаризации AdventureWorks для Поиска Azure

Моделирование структурированного содержимого базы данных для преобразования в эффективный индекс поиска редко бывает простой задачей. Помимо планирования и управления изменениями, существует проблема денормализации исходных строк, присоединенных к таблице, для преобразования в сущности с поддержкой поиска. В этой статье используется пример данных AdventureWorks, доступный в Интернете, чтобы в общих чертах показать, как осуществляется преобразование базы данных для использования поиска. 

## <a name="about-adventureworks"></a>О AdventureWorks

Если у вас есть экземпляр SQL Server, возможно, вы уже использовали пример базы данных AdventureWorks. Среди таблиц, содержащихся в этой базе данных, есть пять таблиц, которые предоставляют сведения о продуктах.

+ **ProductModel**: имя.
+ **Product**: имя, цвет, цена, размер, вес, изображение, категория (каждая строка соединена с конкретным элементом ProductModel).
+ **ProductDescription**: описание.
+ **ProductModelProductDescription**: языковой стандарт (каждая строка соединяет ProductModel с определенным элементом ProductDescription для конкретного языка).
+ **ProductCategory**: имя, родительская категория.

Объединение всех этих данных в плоский набор строк, который можно передать в индекс поиска, и является стоящей перед нами задачей. 

## <a name="considering-our-options"></a>Рассмотрение доступных вариантов

Необдуманный подход заключается в индексировании всех строк из таблицы Product (присоединенных, если это уместно), так как именно таблица Product содержит наиболее информативные сведения. Однако такой подход приведет к тому, что индекс поиска будет содержать очевидные дубликаты в результирующем наборе. Например, модель Road-650 доступна в шести размерах и двух цветах. Запрос "road bikes" выдаст двенадцать экземпляров одной и той же модели, различающихся только размером и цветом. Шесть других дорожных моделей окажутся "на задворках" — на второй странице результатов поиска.

  ![Список продуктов](./media/search-example-adventureworks/products-list.png "Список продуктов")
 
Обратите внимание на то, что для модели Road-650 доступны двенадцать вариантов. Строки сущности "один ко многим" лучше всего могут быть представлены как многозначные поля или поля с предварительно объединенными значениями в индексе поиска.

Решить эту задачу простым перемещением целевого индекса в таблицу ProductModel не получится. Это приведет к пропуску важных данных в таблице Product, которые должны быть представлены в результатах поиска.

## <a name="use-a-collection-data-type"></a>Использование типа данных Collection

Правильный подход — использовать функцию поиска схемы, которая не имеет прямого аналога в модели базы данных: **Collection(Edm.String)**. Тип данных Collection используется в том случае, если у вас есть список отдельных строк, а не очень длинная строка (одна). Если используются ключевые слова или теги, для этого поля лучше использовать тип данных Collection.

Если определить многозначные поля индекса **Collection(Edm.String)** для цвета, размера и изображения, то будут сохранены вспомогательные сведения для применения аспектов и фильтров без засорения индекса повторяющимися записями. Аналогичным образом примените агрегатные функции к числовым полям Product, индексируя **minListPrice** вместо **listPrice** для каждого отдельного продукта.

При индексе с этими структурами поиск фразы "mountain bikes" выдаст разные модели велосипедов, сохраняя такие важные метаданные, как цвет, размер и наименьшая цена. Это показано на следующем снимке экрана.

  ![Пример поиска горного велосипеда](./media/search-example-adventureworks/mountain-bikes-visual.png "Пример поиска горного велосипеда")

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
> [Пример. Таксономии с многоуровневыми аспектами в Поиске Azure](search-example-adventureworks-multilevel-faceting.md)


