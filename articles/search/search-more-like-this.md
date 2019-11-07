---
title: функция запроса moreLikeThis (Предварительная версия)
titleSuffix: Azure Cognitive Search
description: Описание функции moreLikeThis (Предварительная версия), доступной в предварительных версиях REST API Когнитивный поиск Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fdde89f9ff88b15c464af805b81708b268e5ddf5
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721732"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (Предварительная версия) в Azure Когнитивный поиск

> [!IMPORTANT] 
> Эта функция сейчас доступна в виде общедоступной предварительной версии. Функции предварительной версии предоставляются без соглашения об уровне обслуживания и не рекомендуются для рабочих нагрузок. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Эта функция предоставляется в [версии REST API 2019-05-06-Preview](search-api-preview.md). В настоящее время нет поддержки портала или пакета SDK для .NET.

`moreLikeThis=[key]` является параметром запроса в [API поиска документов](https://docs.microsoft.com/rest/api/searchservice/search-documents) , который находит документы, аналогичные документу, указанному ключом документа. При обработке поискового запроса с параметром `moreLikeThis` создается запрос с условиями поиска, извлеченными из заданного документа, которые лучше всего описывают этот документ. Затем созданный запрос используется для создания поискового запроса. По умолчанию учитываются содержимое всех полей, доступных для поиска, за вычетом полей с ограничениями, заданных с помощью параметра `searchFields`. Параметр `moreLikeThis` нельзя использовать с параметром поиска `search=[string]`.

По умолчанию учитываются содержимое всех полей верхнего уровня, поддерживающих поиск. Если вместо этого нужно указать конкретные поля, можно использовать параметр `searchFields`. 

Нельзя использовать moreLikeThis для подполей с возможностью поиска в [сложном типе](search-howto-complex-data-types.md).

## <a name="examples"></a>Примеры 

Ниже приведен пример запроса moreLikeThis. Запрос выполняет поиск документов, поля описания которых сильнее всего похожи на поле исходного документа, как указано в параметре `moreLikeThis`.

```
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2019-05-06-Preview
```

```
POST /indexes/hotels/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "1002",
      "searchFields": "description"
    }
```


## <a name="next-steps"></a>Дальнейшие действия

Для экспериментов с этой функцией можно использовать любое средство веб-тестирования.  Мы рекомендуем использовать POST в этом упражнении.

> [!div class="nextstepaction"]
> [Знакомство с Azure Когнитивный поиск API-интерфейсами RESTFUL с помощью POST](search-get-started-postman.md)