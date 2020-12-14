---
title: Использование простого синтаксиса запроса Lucene
titleSuffix: Azure Cognitive Search
description: Изучите пример, запустив запросы на основе простого синтаксиса полнотекстового поиска, фильтра поиска, географического поиска и поиска по индексу Azure Когнитивный поиск.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/12/2020
ms.openlocfilehash: ff9495e37a499b5502d8f8ced79b69608fa9552a
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401752"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>Использование синтаксиса "простого" поиска в Azure Когнитивный поиск

В Когнитивный поиск Azure [простой синтаксис запроса](query-simple-syntax.md) вызывает средство синтаксического анализа запросов по умолчанию для полнотекстового поиска. Средство синтаксического анализа работает быстро и обрабатывает распространенные сценарии, включая полнотекстовый поиск, фильтрованный и аспектный Поиск, а также Префиксный поиск. В этой статье приводятся примеры, иллюстрирующие простое использование синтаксиса в запросе [поиска документов (REST API)](/rest/api/searchservice/search-documents) .

> [!NOTE]
> Альтернативный синтаксис запросов — это [полная Lucene](query-lucene-syntax.md), поддерживающая более сложные структуры запросов, например поиск нечетких и подстановочных знаков. Дополнительные сведения и примеры см. [в разделе Использование полного синтаксиса Lucene](search-query-lucene-examples.md).

## <a name="nyc-jobs-examples"></a>Примеры заданий Нью

В следующих примерах используется [индекс поиска заданий Нью](https://azjobsdemo.azurewebsites.net/) , состоящий из заданий, доступных на основе набора данных, предоставленного в городе Нью- [Йорк опендата](https://nycopendata.socrata.com/). Эти данные не являются текущими или завершенными. Индекс находится в службе "песочницы", предоставляемой корпорацией Майкрософт. Это означает, что для работы с этими запросами не требуется подписка Azure или Azure Когнитивный поиск.

Для отправки HTTP-запроса по запросу GET или POST требуется соответствующее средство. Если вы не знакомы с этими инструментами, см. статью [Краткое руководство. изучение Azure Когнитивный поиск REST API](search-get-started-rest.md).

## <a name="set-up-the-request"></a>Настройка запроса

1. Заголовки запроса должны иметь следующие значения:

   | Ключ | Значение |
   |-----|-------|
   | Content-Type | `application/json`|
   | api-key  | `252044BE3886FE4A8E3BAA4F595114BB` </br> (это фактический ключ API запроса для службы поиска "песочницы", в которой размещается индекс заданий Нью). |

1. Задайте для команды значение **`GET`** .

1. Задайте для URL-адреса значение **`https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=*&$count=true`** . 

   + Коллекция Documents в индексе содержит все содержимое, доступное для поиска. Ключ API запроса, указанный в заголовке запроса, работает только для операций чтения, предназначенных для коллекции Documents.

   + **`$count=true`** Возвращает количество документов, соответствующих условиям поиска. В пустой строке поиска счетчик будет содержать все документы в индексе (около 2558 в случае заданий Нью).

   + **`search=*`** является неопределенным запросом, эквивалентным null или пустым поиском. Это не особенно полезно, но это самый простой поиск, который можно сделать, и Показать все доступные для получения поля в индексе со всеми значениями.

1. В качестве шага проверки вставьте приведенный ниже запрос в раздел GET и щелкните **Отправить**. Будут возвращены результаты в виде подробных документов JSON.

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-simple-query-parsing"></a>Как вызвать анализ простого запроса

Для интерактивных запросов не нужно указывать дополнительные параметры: простой синтаксис используется по умолчанию. В коде, если был вызван ранее **`queryType=full`** , можно сбросить значение по умолчанию с помощью **`queryType=simple`** .

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "queryType": "simple"
}
```

## <a name="example-1-full-text-search-on-specific-fields"></a>Пример 1. полнотекстовый поиск по конкретным полям

Первый пример не связан с синтаксическим анализатором, но мы начали с него, чтобы представить первое фундаментальное понятие запроса: автономность. Этот пример ограничивает как выполнение запроса, так и ответ на несколько конкретных полей. Знать, как структурировать читаемый ответ JSON, важно, если используется инструмент Postman или обозреватель поиска. 

Этот запрос предназначен только для *business_title* в **`searchFields`** , указывая с помощью **`select`** параметра то же поле в ответе.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Результат запроса должен выглядеть, как на снимке экрана ниже.

  :::image type="content" source="media/search-query-lucene-examples/postman-sample-results.png" alt-text="Пример ответа Postman" border="false":::

Вы могли заметить оценку поиска в ответе. Однородные баллы, равные **1** , происходят, когда нет ранга, так как поиск не является полнотекстовым поиском или не был предоставлен ни один критерий. Для пустого поиска строки возвращаются в произвольном порядке. Когда вы добавите условие поиска, вы увидите, как оценки поиска превратятся в понятные значения.

## <a name="example-2-look-up-by-id"></a>Пример 2. Поиск по идентификатору

При возврате результатов поиска в запросе логический последующий шаг заключается в предоставлении страницы сведений, которая содержит больше полей из документа. В этом примере показано, как вернуть один документ с помощью [операции уточняющего запроса](/rest/api/searchservice/lookup-document) для передачи идентификатора документа.

Все документы имеют уникальный идентификатор. Чтобы проверить синтаксис запроса поиска, сначала получите список идентификаторов документов, чтобы найти в нем нужный идентификатор. В индексе вакансий в Нью-Йорке идентификаторы хранятся в поле `id`.

```http
GET /indexes/nycjobs/docs?api-version=2020-06-30&search=*&$select=id&$count=true
```

Затем извлеките документ из коллекции на основе `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", который был первым в предыдущем ответе. Следующий запрос возвращает все доступные для получения поля для всего документа.

```http
GET /indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2020-06-30
```

## <a name="example-3-filter-queries"></a>Пример 3. Фильтрация запросов

[Синтаксис фильтра](./search-query-odata-filter.md) — это выражение OData, которое можно использовать самостоятельно или с помощью **`search`** . Автономный фильтр без параметра поиска полезен, когда выражение фильтра может полностью определить интересующие документы. Без строки запроса не выполняется лексический или лингвистический анализ (все оценки имеют значение 1), нет оценки и рейтинга. Обратите внимание, что строка поиска пуста.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

Если они используются вместе, сначала ко всему индексу применяется фильтр, а затем в результатах фильтрации выполняется поиск. Поэтому фильтры могут повысить производительность запросов, так как они уменьшают количество документов, которые поисковый запрос должен обработать.

  :::image type="content" source="media/search-query-simple-examples/filtered-query.png" alt-text="Фильтр ответа на запрос" border="false":::

Еще один эффективный способ объединения фильтра и поиска заключается **`search.ismatch*()`** в критерии фильтра, где можно использовать поисковый запрос в фильтре. Это выражение фильтра использует подстановочный знак для термина *план*, чтобы выбрать должность business_title, содержащую термины "план", "планировщик", "планирование" и т. д.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "search.ismatch('plan*', 'business_title', 'full', 'any')",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

Дополнительные сведения о функции см. в [описании search.ismatch в разделе с примерами фильтров](./search-query-odata-full-text-search-functions.md#examples).

## <a name="example-4-range-filters"></a>Пример 4. Фильтры диапазонов

Фильтрация диапазона поддерживается в **`$filter`** выражениях для любого типа данных. В следующих примерах выполняется поиск по числовым и строковым полям. 

Типы данных важны в фильтрах диапазона и лучше всего работают, когда числовые данные находятся в числовых полях, а строковые данные — в строковых. Числовые данные в строковых полях не подходят для диапазонов, так как числовые строки не сравнимы в Azure Когнитивный поиск.

Следующий запрос является числовым диапазоном:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency"
    }
```
Результат запроса должен выглядеть, как на снимке экрана ниже.

  :::image type="content" source="media/search-query-simple-examples/rangefilternumeric.png" alt-text="Фильтр диапазона для числовых диапазонов" border="false":::

В этом запросе диапазон находится над строковым полем (business_title):

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title"
    }
```

Результат запроса должен выглядеть, как на снимке экрана ниже.

  :::image type="content" source="media/search-query-simple-examples/rangefiltertext.png" alt-text="Фильтр диапазона для текстовых диапазонов" border="false":::

> [!NOTE]
> В приложениях поиска часто используется фасетная навигация на основе диапазонов значений. Дополнительные сведения и примеры см. [в разделе Создание фильтра аспектов](search-filters-facets.md).

## <a name="example-5-geo-search"></a>Пример 5. Геопространственный поиск

Индекс выборки включает в себя поле geo_location с координатами широты и долготы. В этом примере используется [функция geo.distance](search-query-odata-geo-spatial-functions.md#examples), которая фильтрует документы в пределах окружности начальной точки до произвольного расстояния (в километрах), которое вы предоставляете. Вы можете отрегулировать последнее значение в запросе (4), чтобы уменьшить или увеличить площадь поверхности запроса.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "business_title, work_location"
    }
```

Для более удобочитаемых результатов результаты поиска будут обрезаны, чтобы они включали название задания и рабочий ресурс. Начальные координаты были получены из случайного документа в индексе (в данном случае для места работы в Статен-Айленде).

  :::image type="content" source="media/search-query-simple-examples/geo-search.png" alt-text="Схема острова Стейтэн" border="false":::

## <a name="example-6-search-precision"></a>Пример 6. Точность поиска

Запросы терминов позволяют искать одиночные термины или наборы терминов, которые оцениваются независимо друг от друга. Запросы фраз заключаются кавычки и проверяются как буквальная строка. Точностью соответствия управляют операторы и параметр searchMode.

Пример 1. `search=fire`  совпадение с результатами в 140, где все совпадения содержат слово Fire где-нибудь в документе.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire"
    }
```

Пример 2. `search=fire department` возвращает 2002 результата. Возвращаются документы, содержащие слово "fire" или "department".

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire department"
    }
```

Пример 3. `search="fire department"` возвращает 77 результатов. При заключении строки в кавычки создается Поиск по фразам, состоящий из обоих терминов, а совпадения обнаруживаются по маркерам в индексе, состоящим из Объединенных терминов. Это объясняет, почему поисковый запрос `search=+fire +department` не является эквивалентным. Оба термина являются обязательными, но их наличие проверяется независимо друг от друга. 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
    "count": true,
    "search": "\"fire department\""
    }
```

> [!Note]
> Поскольку запрос фразы задается в кавычках, в этом примере добавляется escape-символ ( `\` ) для сохранения синтаксиса.

## <a name="example-7-booleans-with-searchmode"></a>Пример 7. Логические операторы с параметром searchMode

Простой синтаксис поддерживает логические операторы в виде знаков (`+, -, |`). Параметр searchMode информирует о компромиссах между точностью и отзывом, при **`searchMode=any`** этом при помощи вызова Call (Match для любого критерия определяется документ для результирующего набора) и **`searchMode=all`** точность (все условия должны быть сопоставлены). 

Значение по умолчанию — **`searchMode=any`** , что может быть запутанным, если вы накладываете запрос с несколькими операторами и получаете более широкие вместо более узких результатов. Это особенно верно для оператора NOT, когда результаты включают в себя все документы, которые "не содержат" конкретный термин.

При использовании searchMode по умолчанию (Any) возвращаются документы 2800: те, которые содержат фразу «пожарный отдел», а также все документы, у которых нет фразы «Метротеч Center».

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "any"
    }
```

Результат запроса должен выглядеть, как на снимке экрана ниже.

  :::image type="content" source="media/search-query-simple-examples/searchmodeany.png" alt-text="режим поиска &quot;любой&quot;" border="false":::

Изменение для **`searchMode=all`** применения совокупного воздействия на критерии и возвращает меньший результирующий набор — 21 документ, состоящий из документов, содержащих целую фразу «пожарный отдел», за вычетом этих заданий по адресу Метротеч Center.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "all"
    }
```

  :::image type="content" source="media/search-query-simple-examples/searchmodeall.png" alt-text="режим поиска &quot;все&quot;" border="false":::

## <a name="example-8-structuring-results"></a>Пример 8. Структурирование результатов

Несколько параметров управляют тем, какие поля добавляются в результаты поиска, числом документов, возвращаемых в каждом пакете, и порядком сортировки. В этом примере переводятся некоторые из предыдущих примеров, ограничивающие результаты конкретными полями с помощью **`$select`** оператора и условий поиска точных выражений, возвращая 82 соответствий.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description"
    }
```

Они добавлены к предыдущему примеру, и их можно отсортировать по заголовку. Сортировка работает, так как поле civil_service_title является *сортируемым* в индексе.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title"
    }
```

Результаты разбиения на страницы реализуются с помощью **`$top`** параметра. в этом случае возвращаются пять первых документов:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5"
    }
```

Чтобы получить следующие 5 документов, пропустите первый пакет.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5",
      "skip": "5"
    }
```

## <a name="next-steps"></a>Дальнейшие действия

Попробуйте указать запросы в коде. Следующие ссылки содержат сведения о настройке поисковых запросов с помощью пакетов SDK для Azure.

+ [Запрос индекса с помощью пакета SDK для .NET](search-get-started-dotnet.md)
+ [Запрос индекса с помощью пакета SDK для Python](search-get-started-python.md)
+ [Запрос индекса с помощью пакета SDK для JavaScript](search-get-started-javascript.md)

Дополнительные справочные материалы по синтаксису, архитектуре запросов и примеры можно найти по следующим ссылкам:

+ [Примеры синтаксиса запросов Lucene для создания расширенных запросов](search-query-lucene-examples.md)
+ [How full text search works in Azure Cognitive Search](search-lucene-query-architecture.md) (Как выполняется полнотекстовый поиск в Когнитивном поиске Azure)
+ [Простой синтаксис запросов](query-simple-syntax.md)
+ [Полный синтаксис запроса Lucene](query-lucene-syntax.md)
+ [Синтаксис фильтра](search-query-odata-filter.md)