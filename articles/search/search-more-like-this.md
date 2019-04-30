---
title: moreLikeThis в службе "Поиск Azure" (предварительная версия)
description: Предварительная документация по компоненту moreLikeThis (предварительная версия), доступному в REST API службы "Поиск Azure".
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 10/27/2016
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d55a6d883e0dcd5ad4b1c1584b76bae06e6c742a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61283371"
---
# <a name="morelikethis-in-azure-search-preview"></a>moreLikeThis в службе "Поиск Azure" (предварительная версия)

`moreLikeThis=[key]` — это параметр запроса в [API поиска](https://docs.microsoft.com/rest/api/searchservice/search-documents). Указав параметр `moreLikeThis` в поисковом запросе, можно выполнить поиск документов, похожих на документ, указанный с помощью ключа. При обработке поискового запроса с параметром `moreLikeThis` создается запрос с условиями поиска, извлеченными из заданного документа, которые лучше всего описывают этот документ. Затем созданный запрос используется для создания поискового запроса. По умолчанию анализируется содержимое всех полей с атрибутом `searchable`, если для ограничения полей не используется параметр `searchFields`. Параметр `moreLikeThis` нельзя использовать с параметром поиска `search=[string]`.

## <a name="examples"></a>Примеры 

Ниже приведен пример запроса moreLikeThis. Запрос выполняет поиск документов, поля описания которых сильнее всего похожи на поле исходного документа, как указано в параметре `moreLikeThis`.

```
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2016-09-01-Preview
```

```
POST /indexes/hotels/docs/search?api-version=2016-09-01-Preview
    {
      "moreLikeThis": "1002",
      "searchFields": "description"
    }
```

## <a name="feature-availability"></a>Доступность функций

Сейчас функция moreLikeThis доступна в предварительной версии и поддерживается только предварительными версиями API `2015-02-28-Preview` и `2016-09-01-Preview`. Так как версия API указывается в запросе, можно совмещать общедоступную и предварительную версии API в одном приложении. Тем не менее предварительные версии API не регулируются соглашением об уровне обслуживания, и их возможности могут измениться, поэтому мы не рекомендуем использовать их в рабочих приложениях.
