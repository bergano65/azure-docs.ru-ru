---
title: moreLikeЭта (предварительная) функция запроса
titleSuffix: Azure Cognitive Search
description: Описывает функцию LikeThis (предварительный просмотр), которая доступна в предварительных версиях API Azure Cognitive Search REST.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 95b9c76a2ff962cb2fa4bacbb1b1e9a953b7014f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873817"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (предварительный просмотр) в Azure когнитивного поиска

> [!IMPORTANT] 
> Эта функция сейчас доступна в виде общедоступной предварительной версии. Для предварительной версии функции соглашение об уровне обслуживания не предусмотрено. Мы не рекомендуем использовать ее в рабочей среде. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Эта функция предоставляется в [версии REST API 2019-05-06-Preview](search-api-preview.md). В настоящее время нет портала или поддержки .NET SDK.

`moreLikeThis=[key]`является параметром запроса в [API поисковых документов,](https://docs.microsoft.com/rest/api/searchservice/search-documents) который находит документы, аналогичные документу, указанному ключом документа. При обработке поискового запроса с параметром `moreLikeThis` создается запрос с условиями поиска, извлеченными из заданного документа, которые лучше всего описывают этот документ. Затем созданный запрос используется для создания поискового запроса. По умолчанию рассматривается содержимое всех областей поиска, за вычетом всех ограниченных полей, указанных с помощью `searchFields` параметра. Параметр `moreLikeThis` нельзя использовать с параметром поиска `search=[string]`.

По умолчанию рассматривается содержимое всех полей поиска верхнего уровня. Если вместо этого требуется указать определенные поля, можно использовать `searchFields` параметр. 

Вы не `MoreLikeThis` можете использовать в поисковых подполях в [сложном типе.](search-howto-complex-data-types.md)

## <a name="examples"></a>Примеры

Все приведенные ниже примеры используютпример отели из проекта [«Быстрый старт: Создайте индекс поиска» на портале Azure.](search-get-started-portal.md)

### <a name="simple-query"></a>Простой запрос

В следующем запросе находятся документы, поле описания которых наиболее `moreLikeThis` похожи на поле исходного документа, указанное параметром:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2019-05-06-Preview
```

В этом примере запрос ищет отели, `HotelId` аналогичные 29.
Вместо того, чтобы использовать HTTP GET, вы также можете вызвать `MoreLikeThis` с помощью HTTP POST:

```
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>Применение фильтров

`MoreLikeThis`могут быть объединены с другими `$filter`общими параметрами запроса, такими как. Например, запрос может быть ограничен только отелями, категорией которых является «Бюджет» и где рейтинг выше 3,5:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2019-05-06-Preview
```

### <a name="select-fields-and-limit-results"></a>Выберите поля и ограничьте результаты

Селектор `$top` может использоваться для ограничения количества результатов, которые должны быть возвращены в запросе. `MoreLikeThis` Кроме того, поля могут `$select`быть выбраны с . Здесь три лучших отеля выбираются вместе с их ID, Имя, и рейтинг: 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2019-05-06-Preview
```

## <a name="next-steps"></a>Дальнейшие действия

Вы можете использовать любой инструмент веб-тестирования для экспериментов с этой функцией.  Мы рекомендуем использовать Почтальон для этого упражнения.

> [!div class="nextstepaction"]
> [Исследуйте ApIs Azure Cognitive Search REST с помощью Postman](search-get-started-postman.md)
