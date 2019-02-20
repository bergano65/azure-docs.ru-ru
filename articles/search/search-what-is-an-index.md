---
title: Создание определения и понятий индекса в службе "Поиск Azure"
description: Общие сведения о терминах и понятиях индексирования в службе "Поиск Azure", в том числе о частях компонентов и физической структуре.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/01/2019
ms.custom: seodec2018
ms.openlocfilehash: 77f4b597ad4b87db7e720dd57191c6b192a4c93b
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000957"
---
# <a name="create-a-basic-index-in-azure-search"></a>Создание простого индекса в службе "Поиск Azure"

*Индекс* в службе "Поиск Azure" — это постоянное хранилище *документов* и других конструкций, используемое для поиска с фильтрацией и полнотекстового поиска этой службой. Документ представляет собой единый блок доступных для поиска данных в индексе. Например, в интернет-магазине для каждой позиции продажи есть определенный документ, в агентстве новостей есть такой документ для каждой статьи и т. д. Можно сравнить эти понятия с более знакомыми понятиями баз данных. В таком случае *индекс* похож на *таблицу*, а *документы* — на *строки* в ней.

Когда вы добавляете или передаете индекс, служба "Поиск Azure" создает физические структуры на основе предоставленной вами схемы. Например, если поле в индексе помечено как доступное для поиска, для него создается инвертированный индекс. Позднее, когда вы добавите или передаете документы или отправите поисковые запросы в службу "Поиск Azure", запросы будут направляться к определенному индексу в службе поиска. Загрузка полей со значениям документов называется *индексированием* или приемом данных.

Индекс можно создать с помощью портала, [REST API](search-create-index-rest-api.md) или [пакета SDK для .NET](search-create-index-dotnet.md).

## <a name="components-of-an-index"></a>Компоненты индекса

Схема индекса в службе "Поиск Azure" содержит описанные ниже элементы. 

[*Коллекция полей*](#fields-collection) обычно будет самой крупной частью индекса. Здесь каждому полю присвоены имя, тип и атрибуты, обозначающие допустимые поведения и порядок использования полей. Также здесь есть такие элементы, как [средства подбора](#suggesters), [профили повышения](#scoring-profiles), [анализаторы](#analyzers) с компонентами для поддержки настройки и параметры [CORS](#cors).

```json
{  
  "name": (optional on PUT; required on POST) "name_of_index",  
  "fields": [  
    {  
      "name": "name_of_field",  
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",  
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),  
      "filterable": true (default) | false,  
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),  
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),  
      "key": true | false (default, only Edm.String fields can be keys),  
      "retrievable": true (default) | false,  
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }  
  ],  
  "suggesters": [  
    {  
      "name": "name of suggester",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": ["field1", "field2", ...]  
    }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "name of scoring profile",  
      "text": (optional, only applies to searchable fields) {  
        "weights": {  
          "searchable_field_name": relative_weight_value (positive #'s),  
          ...  
        }  
      },  
      "functions": (optional) [  
        {  
          "type": "magnitude | freshness | distance | tag",  
          "boost": # (positive number used as multiplier for raw score != 1),  
          "fieldName": "...",  
          "interpolation": "constant | linear (default) | quadratic | logarithmic",  
          "magnitude": {  
            "boostingRangeStart": #,  
            "boostingRangeEnd": #,  
            "constantBoostBeyondRange": true | false (default)  
          },  
          "freshness": {  
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)  
          },  
          "distance": {  
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)  
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)  
          },  
          "tag": {  
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)  
          }  
        }  
      ],  
      "functionAggregation": (optional, applies only when functions are specified)   
        "sum (default) | average | minimum | maximum | firstMatching"  
    }  
  ],  
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",  
  "corsOptions": (optional) {  
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],  
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)  
  }  
}
```

## <a name="fields-collection-and-attribution"></a>Коллекция полей и определение принадлежности
При определении схемы необходимо указать имя, тип и атрибуты для каждого поля в индексе. Тип поля используется для классификации данных, хранящихся в этом поле. Атрибуты устанавливаются для отдельных полей для определения того, как эти поля будут использоваться. Доступные типы и атрибуты перечислены в таблицах ниже.

### <a name="data-types"></a>Типы данных
| type | ОПИСАНИЕ |
| --- | --- |
| *Edm.String* |Текст, который при необходимости может быть снабжен маркером для полнотекстового поиска (разбиение на слова, выделение корней и пр.). |
| *Collection(Edm.String)* |Список строк, которые при необходимости могут быть снабжены маркером для полнотекстового поиска. Для количества элементов в коллекции не предусмотрен теоретический верхний предел, но к коллекциям применяется верхний предел для объема полезных данных — 16 МБ. |
| *Edm.Boolean* |Содержит значения True или False. |
| *Edm.Int32* |32-разрядные целочисленные значения. |
| *Edm.Int64* |64-разрядные целочисленные значения. |
| *Edm.Double* |Числовые данные с двойной точностью. |
| *Edm.DateTimeOffset* |Значения даты и времени в формате OData V4 (например, `yyyy-MM-ddTHH:mm:ss.fffZ` или `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| *Edm.GeographyPoint* |Точка, представляющая географическое расположение. |

Более подробные сведения о поддерживаемых типах данных в службе поиска Azure см. [здесь](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types).

### <a name="index-attributes"></a>Атрибуты индекса
| Атрибут | ОПИСАНИЕ |
| --- | --- |
| *Ключ* |Уникальный строковый идентификатор (ключ) каждого документа, по которому выполняется обращение к документам. У каждого индекса должен быть один ключ. Ключом может быть только одно поле; в качестве его типа должно быть задано значение Edm.String. |
| *Доступный для получения* |Указывает, возвращать ли поле в результатах поиска. |
| *Фильтруемый* |Позволяет использовать поле в запросах фильтра. |
| *Сортируемый* |Позволяет с помощью этого поля сортировать результаты поиска при запросе. |
| *Facetable* |Позволяет использовать поле в структуре [фасетной навигации](search-faceted-navigation.md) для автоматической фильтрации пользователем. Обычно для фасетной навигации подходят поля, содержащие повторяющиеся значения, которые можно использовать для группирования различных документов (например, если несколько документов относятся к одной и той же категории или к одному производителю). |
| *Доступный для поиска* |Помечает поле как доступное для полнотекстового поиска. |

Более подробные сведения об атрибутах индексов в службе поиска Azure см. [здесь](https://docs.microsoft.com/rest/api/searchservice/Create-Index).

## <a name="suggesters"></a>Средства подбора
Средством подбора называется часть схемы, которая определяет, какие поля в индексе используются для поддержки автозавершения и упреждающего ввода запросов при поиске. Как правило, фрагменты запросов передаются в REST API "Предложения" службы "Поиск Azure" по мере ввода текста пользователем, а API возвращает набор предлагаемых фраз. Заданное для индекса средство подбора определяет, какие поля используются для создания условий поиска для опережающего ввода. Дополнительные сведения см. в статье [Add suggesters](index-add-suggesters.md) (Добавление средств подбора).

## <a name="scoring-profiles"></a>Профили оценки

Профилем повышения называется часть схемы, в которой определяются пользовательские механизмы оценки, изменяющие относительную позицию элементов в результатах поиска. Профили оценки состоят из взвешенных полей и функций. Чтобы использовать их, необходимо задать профиль по имени в строке запроса.

Профиль оценки по умолчанию вычисляет оценку поиска для каждого элемента в результирующем наборе в фоновом режиме. Можно использовать внутренний профиль оценки без имени. Можно также задать для параметра defaultScoringProfile использование пользовательского профиля в качестве профиля по умолчанию. Такой профиль будет вызываться всякий раз, когда в строке запроса не указан пользовательский профиль.

Дополнительные сведения см. в статье о [добавлении профилей повышения](index-add-scoring-profiles.md).

## <a name="analyzers"></a>Анализаторы

Элемент анализатора задает имя анализатора языка, который нужно использовать для поля. Допустимый набор значений см. в статье [о языковых анализаторах в службе "Поиск Azure"](index-add-language-analyzers.md). Этот параметр можно использовать только с полями, поддерживающими поиск. Параметр не совместим с элементами **searchAnalyzer** и **indexAnalyzer**. Выбрав для поля анализатор, его невозможно изменить.

## <a name="cors"></a>CORS

По умолчанию клиентский код JavaScript не может обращаться к API, так как браузер блокирует все запросы между разными источниками. Чтобы разрешить запросы между источниками к нужному индексу, включите CORS (Cross-Origin Resource Sharing — общий доступ к ресурсам независимо от источника), установив атрибут **corsOptions**. По соображениям безопасности технологию CORS поддерживают только интерфейсы API запросов. 

Для технологии CORS можно настроить перечисленные ниже параметры.

+ **allowedOrigins** (обязательно). Это список источников, которым будет предоставлен доступ к индексу. Это означает, что любой код JavaScript из такого источника сможет отправлять запросы к этому индексу (при условии, что указан правильный ключ API). Источники здесь обычно задаются в формате `protocol://<fully-qualified-domain-name>:<port>`, хотя `<port>` часто опускается. Дополнительные сведения см. в статье Википедии [о предоставлении ресурсов для нескольких источников](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing).

  Если вы хотите разрешить доступ всем источникам, добавьте в массив **allowedOrigins** единственный элемент `*`. *Этот вариант не рекомендуется для служб поиска в рабочей среде*, но часто он удобен для разработки и отладки.

+ **MaxAgeInSeconds** (необязательно). На основании этого значения браузеры определяют длительность (в секундах) кэширования предварительных ответов CORS. Это значение должно быть целой неотрицательной величиной. Чем оно больше, чем выше производительность, однако при этом применение изменений в политике CORS занимает больше времени. Если это значение не задано, длительность по умолчанию составляет 5 минут.

## <a name="next-steps"></a>Дополнительная информация

Разобравшись со структурой индекса, вы можете перейти на портал и создать свой первый индекс.

> [!div class="nextstepaction"]
> [Создание индекса на портале](search-create-index-portal.md)