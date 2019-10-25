---
title: Фильтры безопасности для усечения результатов
titleSuffix: Azure Cognitive Search
description: Управление доступом к содержимому Когнитивный поиск Azure с помощью фильтров безопасности и удостоверений пользователей.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 24f168f68a60ebb0408b7f1c367039ea5caea6d1
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794277"
---
# <a name="security-filters-for-trimming-results-in-azure-cognitive-search"></a>Фильтры безопасности для усечения результатов в Azure Когнитивный поиск

Фильтры безопасности можно применять для усечения результатов поиска в Azure Когнитивный поиск на основе удостоверения пользователя. В рамках этого поиска, как правило, требуется сравнить личность инициатора поискового запроса с указанными в поле субъектами, у которых есть разрешения на документ. Если совпадение обнаружено, пользователь или субъект (например, группа или роль) имеет доступ к этому документу.

Один из способов реализации фильтров безопасности — через логическое сложение выражений равенства, например, `Id eq 'id1' or Id eq 'id2'` и т. д. Это ненадежный подход, его трудно поддерживать, а в тех случаях, когда список содержит сотни или тысячи значений, замедляется время ответа на запрос (в секундах). 

Использование функции `search.in` является более надежным и простым способом. При использовании `search.in(Id, 'id1, id2, ...')` вместо выражения равенства можно ожидать ответа со временем отклика менее секунды.

В этой статье показано, как реализовать фильтр безопасности, выполнив следующие действия:
> [!div class="checklist"]
> * Создание поля, содержащего идентификаторы субъектов. 
> * Принудительная отправка или обновление имеющихся документов с соответствующими идентификаторами субъектов.
> * Отправка поискового запроса с помощью `search.in` `filter`.

>[!NOTE]
> В этом документе не рассматривается процесс получения идентификаторов субъектов. Его следует узнать у поставщика службы идентификации.

## <a name="prerequisites"></a>Технические условия

В этой статье предполагается, что у вас есть [Подписка Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), [Служба когнитивный Поиск azure](https://docs.microsoft.com/azure/search/search-create-service-portal)и [индекс когнитивный Поиск Azure](https://docs.microsoft.com/azure/search/search-create-index-portal).  

## <a name="create-security-field"></a>Создание поля безопасности

Документы должны включать поле со списком групп, у которых есть доступ. Эта информация выступает условием фильтрации, по которому документы выбираются или отклоняются из результирующего набора, возвращаемого инициатору запроса.
Предположим, что у нас есть индекс защищенных файлов, и каждый файл доступен разным наборам пользователей.
1. Добавьте поле `group_ids` (здесь можно выбрать любое имя) в качестве `Collection(Edm.String)`. Задайте для атрибута `filterable` этого поля значение `true`. В таком случае результаты поиска фильтруются на основе прав доступа пользователя. Например, если в поле `group_ids` задать значение `["group_id1, group_id2"]` для документа со свойством `file_name` "secured_file_b", разрешение на чтение файла имеют только пользователи, принадлежащие к группам "group_id1" или "group_id2".
   Убедитесь, что для атрибута поля `retrievable` задано значение `false`, чтобы пользователи не могли извлечь файлы в рамках поискового запроса.
2. Кроме того, добавьте поля `file_id` и `file_name` для данного примера.  

```JSON
{
    "name": "securedfiles",  
    "fields": [
        {"name": "file_id", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "file_name", "type": "Edm.String"},
        {"name": "group_ids", "type": "Collection(Edm.String)", "filterable": true, "retrievable": false}
    ]
}
```

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Принудительная отправка данных в индекс с использованием REST API
  
Отправьте запрос HTTP POST к конечной точке URL-адреса индекса. Текст HTTP-запроса является объектом JSON, содержащим документы, которые нужно добавить.

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=2019-05-06  
Content-Type: application/json
api-key: [admin key]
```

В тексте запроса укажите содержимое документов:

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "file_id": "1",
            "file_name": "secured_file_a",
            "group_ids": ["group_id1"]
        },
        {
            "@search.action": "upload",
            "file_id": "2",
            "file_name": "secured_file_b",
            "group_ids": ["group_id1", "group_id2"]
        },
        {
            "@search.action": "upload",
            "file_id": "3",
            "file_name": "secured_file_c",
            "group_ids": ["group_id5", "group_id6"]
        }
    ]
}
```

Если нужно обновить для имеющегося документа список групп, можно использовать действие `merge` или `mergeOrUpload`.

```JSON
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "file_id": "3",
            "group_ids": ["group_id7", "group_id8", "group_id9"]
        }
    ]
}
```

Полные сведения о добавлении или обновлении документов см. в статье [об изменении документов](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>Применение фильтра безопасности

Чтобы сократить количество документов на основе доступа по `group_ids`, выполните поисковой запрос с использованием фильтра `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` (где "group_id1 group_id2,..." — это группы, к которым принадлежит инициатор запроса).
Этот фильтр соответствует всем документам, для которых поле `group_ids` содержит один из заданных идентификаторов.
Подробные сведения о поиске документов с помощью Когнитивный поиск Azure можно [найти в документах поиска](https://docs.microsoft.com/rest/api/searchservice/search-documents).
Обратите внимание, что в этом примере показано, как осуществлять поиск документов с помощью запроса POST.

Выполните запрос HTTP POST:

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=2019-05-06
Content-Type: application/json  
api-key: [admin or query key]
```

В тексте запроса укажите фильтр:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

Будут возвращены документы, в поле `group_ids` которых содержится "group_id1" или "group_id2". Другими словами, вы получаете документы, к которым у инициатора запроса есть доступ на чтение.

```JSON
{
 [
   {
    "@search.score":1.0,
     "file_id":"1",
     "file_name":"secured_file_a",
   },
   {
     "@search.score":1.0,
     "file_id":"2",
     "file_name":"secured_file_b"
   }
 ]
}
```
## <a name="conclusion"></a>Заключение

Вот как можно фильтровать результаты на основе удостоверения пользователя и функции Azure Когнитивный поиск `search.in()`. Эту функцию можно использовать для передачи идентификаторов принципов запрашивающего пользователя для сопоставления с идентификаторами участников, связанными с каждым целевым документом. Во время обработки поискового запроса функция `search.in` отфильтровывает результаты поиска, отклоняя файлы, для которых ни один из субъектов-пользователей не имеет доступа на чтение. Идентификаторы субъектов могут представлять группы безопасности, роли или даже удостоверение пользователя.
 
## <a name="see-also"></a>Дополнительные материалы

+ [Active Directory управления доступом на основе удостоверений с помощью фильтров Когнитивный поиск Azure](search-security-trimming-for-azure-search-with-aad.md)
+ [Фильтры в Когнитивный поиск Azure](search-filters.md)
+ [Безопасность данных и управление доступом в Azure Когнитивный поиск операции](search-security-overview.md)