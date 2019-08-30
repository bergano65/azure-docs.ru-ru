---
title: moreLikeThis в службе "Поиск Azure" (предварительная версия)
description: Предварительная документация по компоненту moreLikeThis (предварительная версия), доступному в REST API службы "Поиск Azure".
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d7c816c545c6647907aa9d700a4eb6ed91277465
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182298"
---
# <a name="morelikethis-in-azure-search"></a>moreLikeThis в службе поиска Azure

> [!Note]
> moreLikeThis находится на этапе предварительной версии и не предназначен для использования в рабочей среде. Эта функция предоставляется в [версии REST API 2019-05-06-Preview](search-api-preview.md). Поддержка пакета SDK для .NET пока не реализована.

`moreLikeThis=[key]`параметр запроса в [API поиска документов](https://docs.microsoft.com/rest/api/searchservice/search-documents) , который находит документы, аналогичные документу, указанному ключом документа. При обработке поискового запроса с параметром `moreLikeThis` создается запрос с условиями поиска, извлеченными из заданного документа, которые лучше всего описывают этот документ. Затем созданный запрос используется для создания поискового запроса. По умолчанию учитываются содержимое всех полей, доступных для поиска, за вычетом полей с ограниченным доступом, заданных с помощью `searchFields` параметра. Параметр `moreLikeThis` нельзя использовать с параметром поиска `search=[string]`.

По умолчанию учитываются содержимое всех полей верхнего уровня, поддерживающих поиск. Если вместо этого нужно указать конкретные поля, можно использовать `searchFields` параметр. 

Нельзя использовать moreLikeThis для подполей с возможностью поиска в сложном [типе](search-howto-complex-data-types.md).

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


## <a name="next-steps"></a>Следующие шаги

Для экспериментов с этой функцией можно использовать любое средство веб-тестирования.  Мы рекомендуем использовать POST в этом упражнении.

> [!div class="nextstepaction"]
> [Знакомство с API-интерфейсами службы "Поиск Azure" с помощью POST](search-get-started-postman.md)