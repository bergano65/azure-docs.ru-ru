---
title: Добавление средств подбора в индекс службы "Поиск Azure"
description: Включает поля для действий упреждающих запросов, когда предлагаемые запросы состоят из текста полей в индексе службы "Поиск Azure".
ms.date: 02/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 7128e4d3b0675775dc713451ef672b28a4991499
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56269932"
---
# <a name="add-suggesters-to-an-azure-search-index"></a>Добавление средств подбора в индекс службы "Поиск Azure"

**Средство подбора** — это конструкция Поиска Azure, поддерживающая функции [Предложения](https://docs.microsoft.com/rest/api/searchservice/suggestions) "поиск по мере ввода" и [автозаполнение (предварительная версия)](search-autocomplete-tutorial.md). Прежде чем вызывать API предложений, вы должны определить **средство подбора** в индексе, чтобы включить предложения для определенных полей.

Несмотря на то что **средство подбора** имеет несколько свойств, в первую очередь это коллекция полей, для которых вы включаете [API предложений](https://docs.microsoft.com/rest/api/searchservice/suggestions). Например, для приложения для путешествий необходимо включить упреждающий поиск по пунктам назначения, городам и достопримечательностям. Таким образом все три поля попадут в коллекцию.

У вас может быть только один ресурс **средства подбора** для каждого индекса (в частности, одно **средство подбора** в коллекции **suggesters**).

## <a name="creating-a-suggester"></a>Создание средства подбора 

Вы можете создать **средство подбора** в любое время, но влияние на ваш индекс зависит от полей. 

+ Новые поля, добавленные в средство подбора в качестве того же обновления, являются наименее эффективными, поскольку не требуют перестройки индекса.
+ Но добавление в средство подбора существующих полей изменяет их определение, что требует полной перестройки индекса.

 **Средства подбора** лучше всего работают при поиске предложений по определенным документам, а не по произвольным условиям поиска и фразам. Они хорошо обрабатывают такие поля, как заголовки, имена и другие относительно короткие фразы, идентифицирующие документ. Менее эффективно выполняется поиск по таким полям с множеством повторяющихся элементов, как категории и теги, а также по очень длинным полям, таким как описания и комментарии.  

После создания средства подбора добавьте [API предложений](https://docs.microsoft.com/rest/api/searchservice/suggestions) в логику запроса, чтобы вызвать функцию.  

Указанные ниже свойства определяют **средство подбора**.  

|Свойство|ОПИСАНИЕ|  
|--------------|-----------------|  
|`name`|Имя **средства подбора**. Используйте имя **средства подбора** при вызове [Предложения &#40;REST API Службы поиска Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/suggestions).|  
|`searchMode`|Стратегия, используемая для поиска фраз кандидата. В настоящее время поддерживается только режим `analyzingInfixMatching`, в рамках которого выполняется гибкий поиск совпадений в начале и середине фраз.|  
|`sourceFields`|Список из одного или нескольких полей, которые служат источником содержимого для предложений. Источниками могут быть только поля типов `Edm.String` и `Collection(Edm.String)`. Можно использовать только поля, для которых не задан настраиваемый языковой анализатор. |  

## <a name="suggester-example"></a>Пример средства подбора  
 **Средство подбора** — это часть определения индекса. В текущей версии коллекции **suggesters** может существовать только одно **средство подбора**, наряду с коллекцией **fields** и **scoringProfiles**.  

```  
{  
  "name": "hotels",  
  "fields": [  
     . . .   
   ],  
  "suggesters": [  
    {  
    "name": "sg",  
    "searchMode": "analyzingInfixMatching",  
    "sourceFields": ["hotelName", "category"]  
    }  
  ],  
  "scoringProfiles": [  
     . . .   
  ]  
}  

```  

## <a name="see-also"></a>См. также  
 [Create Index &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  (Создание индекса (REST API Службы поиска Azure))  
 [Update Index &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/update-index)  (Обновление индекса (REST API Службы поиска Azure))  
 [Suggestions &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/suggestions)  (Предложения (REST API Службы поиска Azure))  
 [Index operations &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/index-operations)  (Операции с индексами (REST API Службы поиска Azure))  
 [Azure Search Service REST](https://docs.microsoft.com/rest/api/searchservice/)  (REST Службы поиска Azure)  
 [SDK .NET службы поиска Azure](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
