---
title: Реляционные данные по модели S'L для импорта и индексации
titleSuffix: Azure Cognitive Search
description: Узнайте, как моделировать реляционные данные, де-нормализации в плоский набор результатов, для индексации и полного поиска текста в Azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3b973dd05d23d190c77986ca9bf6d39656739cd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72790095"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>Как моделировать реляционные данные S'L для импорта и индексации в Azure Cognitive Search

Azure Cognitive Search принимает плоский рядок в качестве ввода в [конвейер индексирования.](search-what-is-an-index.md) Если исходные данные получены из объединенных таблиц в реляционной базе данных S'L Server, в этой статье объясняется, как построить набор результатов и как смоделировать отношения родителей и дочерних групп в индексе когнитивного поиска Azure.

В качестве иллюстрации мы будем ссылаться на гипотетическую базу данных отелей, основанную на [демо-данных.](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels) Предположим, что база данных состоит из таблицы Hotels$ с 50 отелями и стола Rooms$ с номерами различных типов, тарифов и удобств, на общую сумму 750 номеров. Между таблицами существует взаимосвязь между одним и многими. В нашем подходе представление предоставит запрос, который возвращает 50 строк, один ряд в отеле, с сопутствуемыми деталями номера, встроенными в каждый ряд.

   ![Таблицы и просмотр в базе данных отелей](media/index-sql-relational-data/hotels-database-tables-view.png "Таблицы и просмотр в базе данных отелей")


## <a name="the-problem-of-denormalized-data"></a>Проблема денормализации данных

Одна из проблем в работе с отношениями «один к много» заключается в том, что стандартные запросы, построенные на объединенных таблицах, возвращают денормализационные данные, что не работает хорошо в сценарии когнитивного поиска Azure. Рассмотрим следующий пример, который присоединяется к отелям и номерам.

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
Результаты этого запроса возвращают все поля отеля, за которыми следуют все поля номеров, при этом предварительная информация об отеле повторяется для каждого значения номера.

   ![Денормализованные данные, избыточные данные об отелях при добавлении полей номеров](media/index-sql-relational-data/denormalize-data-query.png "Денормализованные данные, избыточные данные об отелях при добавлении полей номеров")


Хотя этот запрос успешно работает на поверхности (предоставляя все данные в наборе плоских строк), он не обеспечивает правильную структуру документа для ожидаемого поиска. Во время индексирования Azure Cognitive Search создаст по одному поисковому документу для каждой строки, просачивающейся. Если бы ваши поисковые документы выглядели как вышеуказанные результаты, вы бы воспринимали дубликаты - семь отдельных документов только для отеля Twin Dome. Запрос на "отели во Флориде" будет возвращать семь результатов только для Twin Dome отель, толкая других соответствующих отелей глубоко в результатах поиска.

Чтобы получить ожидаемый опыт одного документа в отеле, вы должны предоставить rowset на право детализации, но с полной информацией. К счастью, вы можете сделать это легко, приняв методы в этой статье.

## <a name="define-a-query-that-returns-embedded-json"></a>Определение запроса, который возвращает встроенный JSON

Чтобы обеспечить ожидаемый опыт поиска, набор данных должен состоять из одной строки для каждого поискового документа в Azure Cognitive Search. В нашем примере мы хотим один ряд для каждого отеля, но мы также хотим, чтобы наши пользователи могли искать на других областях, связанных с номерами, которые они заботятся о, таких как ночной тариф, размер и количество кроватей, или вид на пляж, все из которых являются частью деталей комнаты.

Решение состоит в том, чтобы захватить детали комнаты как вложенные JSON, а затем вставить структуру JSON в поле в поле, как показано на втором этапе. 

1. Предположим, у вас есть два объединенных таблицы, Hotels$ и Rooms$, которые содержат детали для 50 отелей и 750 номеров, и соединены на поле HotelID. Индивидуально эти столы содержат 50 отелей и 750 связанных номеров.

    ```sql
    CREATE TABLE [dbo].[Hotels$](
      [HotelID] [nchar](10) NOT NULL,
      [HotelName] [nvarchar](255) NULL,
      [Description] [nvarchar](max) NULL,
      [Description_fr] [nvarchar](max) NULL,
      [Category] [nvarchar](255) NULL,
      [Tags] [nvarchar](255) NULL,
      [ParkingIncluded] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [LastRenovationDate] [smalldatetime] NULL,
      [Rating] [float] NULL,
      [StreetAddress] [nvarchar](255) NULL,
      [City] [nvarchar](255) NULL,
      [State] [nvarchar](255) NULL,
      [ZipCode] [nvarchar](255) NULL,
      [GeoCoordinates] [nvarchar](255) NULL
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
    GO

    CREATE TABLE [dbo].[Rooms$](
      [HotelID] [nchar](10) NULL,
      [Description] [nvarchar](255) NULL,
      [Description_fr] [nvarchar](255) NULL,
      [Type] [nvarchar](255) NULL,
      [BaseRate] [float] NULL,
      [BedOptions] [nvarchar](255) NULL,
      [SleepsCount] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [Tags] [nvarchar](255) NULL
    ) ON [PRIMARY]
    GO
    ```

2. Создайте представление, состоящее из всех`SELECT * from dbo.Hotels$`полей в родительской таблице ( ) с добавлением нового поля *Rooms,* содержащего выход вложенного запроса. Положение **ДЛЯ JSON** `SELECT * from dbo.Rooms$` AUTO о структурах вывода как JSON. 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   На следующем скриншоте показан полученный вид, с полем *Rooms* nvarchar внизу. Поле *номеров* существует только в виде HotelRooms.

   ![Вид на гостиничные номера](media/index-sql-relational-data/hotelsrooms-view.png "Вид HoteRooms")

1. Выполнить, `SELECT * FROM dbo.HotelRooms` чтобы получить набор строк. Этот запрос возвращает 50 строк, по одному на отель, с информацией о связанных с номерами в качестве коллекции JSON. 

   ![Rowset из HotelRooms вид](media/index-sql-relational-data/hotelrooms-rowset.png "Rowset из HotelRooms вид")

Эта строка готова к импорту в Azure Cognitive Search.

> [!NOTE]
> Этот подход предполагает, что встроенный JSON находится под [максимальными ограничениями размера столбца сервера S'L Server.](https://docs.microsoft.com/sql/sql-server/maximum-capacity-specifications-for-sql-server) Если данные не подходят, вы можете попробовать программный подход, как показано в [примере: Смоделируйте базу данных AdventureWorks Inventory для Azure Cognitive Search.](search-example-adventureworks-modeling.md)

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>Используйте сложную коллекцию для «многих» отношений

На стороне Azure Cognitive Search создайте схему индекса, которая моделирует отношения «один к много» с помощью вложенного JSON. Созданный в предыдущем разделе набор результатов в целом соответствует приведенной ниже схеме индекса (мы вырезаем некоторые поля для краткости).

Следующий пример аналогичен примеру в [какой моделировать сложные типы данных.](search-howto-complex-data-types.md#creating-complex-fields) *Структура номеров,* которая была в центре внимания этой статьи, находится в поле сбора индекса имени *гостиниц*. Этот пример также показывает сложный тип для *Адреса*, который отличается от *номеров* тем, что он состоит из фиксированного набора элементов, в отличие от многократного произвольного количества элементов, разрешенных в коллекции.

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
        { "name": "BedOptions", "type": "Edm.String", "searchable": true, "filterable": true, "facetable": true },
        { "name": "SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
        { "name": "SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
        { "name": "Tags", "type": "Edm.Collection", "searchable": true }
      ]
    }
  ]
}
```

Учитывая предыдущий набор результатов и вышеупомянутую схему индекса, у вас есть все необходимые компоненты для успешной операции индексирования. Уплощенный набор данных отвечает требованиям индексирования, но сохраняет подробную информацию. В индексе когнитивного поиска Azure результаты поиска легко попадут в объекты, основанные на гостиницах, сохраняя при этом контекст отдельных номеров и их атрибуты.

## <a name="next-steps"></a>Дальнейшие действия

Используя свой собственный набор данных, можно использовать [мастер данных Импорта](search-import-data-portal.md) для создания и загрузки индекса. Мастер обнаруживает встроенную коллекцию JSON, например коллекцию, содержащуюся в *номерах,* и делает схему индекса, которая включает в себя сложную коллекцию типов. 

  ![Индекс, выведенный мастером данных Импорта](media/index-sql-relational-data/search-index-rooms-complex-collection.png "Индекс, выведенный мастером данных Импорта")

Попробуйте следующий быстрый запуск, чтобы узнать основные шаги мастера данных импорта.

> [!div class="nextstepaction"]
> [Быстрый запуск: Создайте индекс поиска с помощью портала Azure](search-get-started-portal.md)