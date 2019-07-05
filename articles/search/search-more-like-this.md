---
title: moreLikeThis в службе "Поиск Azure" (предварительная версия)
description: Предварительная документация по компоненту moreLikeThis (предварительная версия), доступному в REST API службы "Поиск Azure".
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 4d1c691e570d3cfc7e0475c02e4c60ed6ffa8440
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485354"
---
# <a name="morelikethis-in-azure-search"></a>moreLikeThis в службе поиска Azure

> [!Note]
> moreLikeThis находится в предварительной версии и не предназначена для использования в рабочей среде. Эта функция предоставляется в [версии REST API 2019-05-06-Preview](search-api-preview.md). Поддержка пакета SDK для .NET пока не реализована.

`moreLikeThis=[key]` — Это параметр запроса в [API поиска документов](https://docs.microsoft.com/rest/api/searchservice/search-documents) , поиск документов, аналогичную документ, указанный с помощью ключа. При обработке поискового запроса с параметром `moreLikeThis` создается запрос с условиями поиска, извлеченными из заданного документа, которые лучше всего описывают этот документ. Затем созданный запрос используется для создания поискового запроса. По умолчанию считаются содержимое всех полей, поддерживающих поиск, за вычетом ограниченных полей, которые определены с помощью `searchFields` параметра. Параметр `moreLikeThis` нельзя использовать с параметром поиска `search=[string]`.

По умолчанию считаются все поля верхнего уровня для поиска. Если вы хотите вместо этого указать конкретные поля, можно использовать `searchFields` параметра. 

MoreLikeThis нельзя использовать для поиска вложенных полей в [сложного типа](search-howto-complex-data-types.md).

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

Можно использовать любые веб-инструмент тестирования для экспериментов с помощью этой функции.  Мы рекомендуем использовать Postman для этого упражнения.

> [!div class="nextstepaction"]
> [Изучение API REST поиска Azure, с помощью Postman](search-get-started-postman.md)