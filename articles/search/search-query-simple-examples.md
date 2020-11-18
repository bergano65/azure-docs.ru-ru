---
title: Создание простого запроса
titleSuffix: Azure Cognitive Search
description: Изучите пример, запустив запросы на основе простого синтаксиса полнотекстового поиска, фильтра поиска, географического поиска и поиска по индексу Azure Когнитивный поиск.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 834e4fe8c7b3923f40a07c02c0310200db222308
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697260"
---
# <a name="create-a-simple-query-in-azure-cognitive-search"></a>Создание простого запроса в Azure Когнитивный поиск

В Когнитивный поиск Azure [простой синтаксис запроса](query-simple-syntax.md) вызывает средство синтаксического анализа запросов по умолчанию для выполнения запросов полнотекстового поиска по индексу. Это средство синтаксического анализа работает быстро и обрабатывает распространенные сценарии, включая полнотекстовый поиск, фильтрованный и аспектный Поиск, а также географический поиск. 

В этой статье мы будем использовать примеры для иллюстрации простого синтаксиса, заполняя `search=` параметр операции [поиска документов](/rest/api/searchservice/search-documents) .

Альтернативный синтаксис запросов — это [полная Lucene](query-lucene-syntax.md), поддерживающая более сложные структуры запросов, например поиск нечетких и подстановочных знаков, для обработки которых может потребоваться дополнительное время. Дополнительные сведения и примеры, демонстрирующие полный синтаксис, см. [в разделе Использование полного синтаксиса Lucene](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>Формирование запросов в Postman

В следующих примерах используется индекс поиска вакансий в Нью-Йорке, состоящий из вакансий, доступных на основе набора данных, полученных в рамках инициативы [City of New York OpenData](https://nycopendata.socrata.com/). Эти данные не являются текущими или завершенными. Индекс находится в службе "песочницы", предоставляемой корпорацией Майкрософт. Это означает, что для работы с этими запросами не требуется подписка Azure или Azure Когнитивный поиск.

Вам потребуется Postman или аналогичный инструмент для отправки HTTP-запроса GET. Дополнительные сведения см. в разделе [Краткое руководство. изучение REST API когнитивный Поиск Azure](search-get-started-rest.md).

### <a name="set-the-request-header"></a>Настройка заголовка запроса

1. В заголовке запроса задайте значение **Content-Type** для `application/json`.

2. Добавьте элемент **api-key** и задайте для него следующую строку: `252044BE3886FE4A8E3BAA4F595114BB`. Это ключ запроса для службы поиска в песочнице, в которой размещен индекс поиска вакансий Нью-Йорка.

Настроенный заголовок запроса можно многократно использовать для всех запросов в этой статье, только заменяя строку **search=**. 

  :::image type="content" source="media/search-query-lucene-examples/postman-header.png" alt-text="Параметры набора заголовков запроса POST" border="false":::

### <a name="set-the-request-url"></a>Настройка URL-адреса запроса

Запрос — это команда GET с URL-адресом, содержащим конечную точку Когнитивный поиск Azure и строку поиска.

  :::image type="content" source="media/search-query-lucene-examples/postman-basic-url-request-elements.png" alt-text="Запрос POST заголовка GET" border="false":::

URL-адрес содержит следующие элементы.

+ **`https://azs-playground.search.windows.net/`** — это служба поиска "песочницы", поддерживаемая командой разработчиков Когнитивный поиск Azure. 
+ **`indexes/nycjobs/`** Индекс заданий Нью в коллекции индексов этой службы. Имя службы и индекс должны быть указаны в запросе.
+ **`docs`** — Это коллекция Documents, содержащая все содержимое, доступное для поиска. Значение api-key в заголовке запроса подходит только для операций чтения, нацеленных на эту коллекцию документов.
+ **`api-version=2020-06-30`** Задает версию API, которая является обязательным параметром при каждом запросе.
+ **`search=*`** Строка запроса, которая в исходном запросе имеет значение null, возвращает первые 50 результатов (по умолчанию).

## <a name="send-your-first-query"></a>Отправка первого запроса

В качестве шага проверки вставьте приведенный ниже запрос в раздел GET и щелкните **Отправить**. Будут возвращены результаты в виде подробных документов JSON. Возвращаются все документы, что позволяет просматривать все поля и все значения.

Вставьте этот URL-адрес в клиент RESTFUL в качестве шага проверки и просмотрите структуру документа.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*
  ```

Строка запроса, **`search=*`** , является неопределенным поиском, эквивалентом null или пустым поиском. Этот запрос не очень полезен, но это самый простой поиск, который можно выполнить.

При необходимости можно добавить **`$count=true`** в URL-адрес, чтобы вернуть число документов, соответствующих условиям поиска. При пустой строке поиска это будут все документы в индексе (примерно 2800 в случае вакансий в Нью-Йорке).

## <a name="how-to-invoke-simple-query-parsing"></a>Как вызвать анализ простого запроса

Для интерактивных запросов не нужно указывать дополнительные параметры: простой синтаксис используется по умолчанию. В коде, если вы ранее вызывали **queryType=full** для применения полного синтаксиса запросов, можно вернуть значение по умолчанию, **queryType=simple**.

## <a name="example-1-field-scoped-query"></a>Пример 1. Запрос по полю

Первый пример не связан с синтаксическим анализатором, но мы начали с него, чтобы представить первое фундаментальное понятие запроса: автономность. В этом примере задается область выполнения запроса и для ответа определяется всего несколько конкретных полей. Знать, как структурировать читаемый ответ JSON, важно, если используется инструмент Postman или обозреватель поиска. 

Для краткости запрос нацелен только на поле *business_title* и указывает возвращаемые должности. Синтаксис **searchFields** ограничивает выполнение запроса только полем business_title и **выбирает**, какие поля должны быть включены в ответ.

### <a name="partial-query-string"></a>Частичная строка запроса

```http
searchFields=business_title&$select=business_title&search=*
```

Ниже приведен тот же запрос с несколькими полями в списке, разделенном запятыми.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>Полный URL-адрес

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchFields=business_title&$select=business_title&search=*
```

Результат запроса должен выглядеть, как на снимке экрана ниже.

  :::image type="content" source="media/search-query-lucene-examples/postman-sample-results.png" alt-text="Пример ответа Postman" border="false":::

Вы могли заметить оценку поиска в ответе. Универсальная оценка 1 отображается, если приоритет не указан, потому что выполнен поиск не всего текста или не указано условие. Для нулевого поиска без критериев строки возвращаются в произвольном порядке. Когда вы добавите условие поиска, вы увидите, как оценки поиска превратятся в понятные значения.

## <a name="example-2-look-up-by-id"></a>Пример 2. Поиск по идентификатору

Это немного нетипичный пример. Однако при оценке поведения при поиске может потребоваться проверить все содержимое конкретного документа, чтобы понять, почему он был включен в результаты или исключен из них. Чтобы вернуть весь документ, используйте [операцию поиска](/rest/api/searchservice/lookup-document) для передачи идентификатора документа.

Все документы имеют уникальный идентификатор. Чтобы проверить синтаксис запроса поиска, сначала получите список идентификаторов документов, чтобы найти в нем нужный идентификатор. В индексе вакансий в Нью-Йорке идентификаторы хранятся в поле `id`.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchFields=id&$select=id&search=*
```

В следующем примере выполняется запрос поиска, возвращающий определенный документ по значению `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", которое было указано первым в предыдущем ответе. Следующий запрос возвращает весь документ, а не отдельные поля. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2020-06-30&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>Пример 3. Фильтрация запросов

[Синтаксис фильтра](./search-query-odata-filter.md) — это выражение OData, которое можно использовать с параметром **search** или само по себе. Автономный фильтр без параметра поиска полезен, когда выражение фильтра может полностью определить интересующие документы. Без строки запроса не выполняется лексический или лингвистический анализ (все оценки имеют значение 1), нет оценки и рейтинга. Обратите внимание, что строка поиска пуста.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Если они используются вместе, сначала ко всему индексу применяется фильтр, а затем в результатах фильтрации выполняется поиск. Поэтому фильтры могут повысить производительность запросов, так как они уменьшают количество документов, которые поисковый запрос должен обработать.

  :::image type="content" source="media/search-query-simple-examples/filtered-query.png" alt-text="Фильтр ответа на запрос" border="false":::

Если вы хотите попробовать это в Postman, используя GET, вы можете вставить эту строку:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

Еще один эффективный способ объединения фильтра и поиска заключается **`search.ismatch*()`** в критерии фильтра, где можно использовать поисковый запрос в фильтре. Это выражение фильтра использует подстановочный знак для термина *план*, чтобы выбрать должность business_title, содержащую термины "план", "планировщик", "планирование" и т. д.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

Дополнительные сведения о функции см. в [описании search.ismatch в разделе с примерами фильтров](./search-query-odata-full-text-search-functions.md#examples).

## <a name="example-4-range-filters"></a>Пример 4. Фильтры диапазонов

Фильтрация диапазона поддерживается в **`$filter`** выражениях для любого типа данных. В следующих примерах выполняется поиск по числовым и строковым полям. 

Типы данных важны в фильтрах диапазона и лучше всего работают, когда числовые данные находятся в числовых полях, а строковые данные — в строковых. Числовые данные в строковых полях не подходят для диапазонов, так как числовые строки не сравнимы в Azure Когнитивный поиск. 

Следующие примеры приведены в формате POST для удобства чтения (числовой диапазон, за которым следует текстовый диапазон):

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency",
      "count": "true"
    }
```
  :::image type="content" source="media/search-query-simple-examples/rangefilternumeric.png" alt-text="Фильтр диапазона для числовых диапазонов" border="false":::


```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  :::image type="content" source="media/search-query-simple-examples/rangefiltertext.png" alt-text="Фильтр диапазона для текстовых диапазонов" border="false":::

Вы также можете попробовать их в Postman, используя GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> В приложениях поиска часто используется фасетная навигация на основе диапазонов значений. Дополнительные сведения и примеры создания фильтров для структур фасетной навигации см. в разделе ["Фильтрация по диапазону значений" в статье *Как реализовать фасетную навигацию в службе поиска Azure*](search-faceted-navigation.md#filter-based-on-a-range).

## <a name="example-5-geo-search"></a>Пример 5. Геопространственный поиск

Индекс выборки включает в себя поле geo_location с координатами широты и долготы. В этом примере используется [функция geo.distance](./search-query-odata-geo-spatial-functions.md#examples), которая фильтрует документы в пределах окружности начальной точки до произвольного расстояния (в километрах), которое вы предоставляете. Вы можете отрегулировать последнее значение в запросе (4), чтобы уменьшить или увеличить площадь поверхности запроса.

Следующий пример приведен в формате POST для удобства чтения:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
Для более удобочитаемых результатов результаты поиска усекаются, чтобы включить идентификатор задания, должность и рабочий ресурс. Начальные координаты были получены из случайного документа в индексе (в данном случае для места работы в Статен-Айленде).

Вы также можете попробовать их в Postman, используя GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>Пример 6. Точность поиска

Запросы терминов позволяют искать одиночные термины или наборы терминов, которые оцениваются независимо друг от друга. Запросы фраз заключаются кавычки и проверяются как буквальная строка. Точностью соответствия управляют операторы и параметр searchMode.

Пример 1. **`&search=fire`**  возвращает 150 результатов, где все совпадения содержат слово Fire где-нибудь в документе.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=fire
```

Пример 2. **`&search=fire department`** возвращает 2002 результатов. Возвращаются документы, содержащие слово "fire" или "department".

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=fire department
```

Пример 3. **`&search="fire department"`** возвращает 82 результатов. Заключение строки в кавычки позволяет выполнить буквальный поиск обоих терминов, и в индексе, состоящем из объединенных терминов, найдены совпадения с терминами, снабженными маркером. Это объясняет, почему поиск **`search=+fire +department`** , например, не эквивалентен. Оба термина являются обязательными, но их наличие проверяется независимо друг от друга. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>Пример 7. Логические операторы с параметром searchMode

Простой синтаксис поддерживает логические операторы в виде знаков (`+, -, |`). Параметр searchMode задает компромисс между точностью и полнотой результатов. `searchMode=any` позволяет отдать предпочтение полноте (в результирующий набор добавляется любой документ, соответствующий любому заданному условию), а `searchMode=all` позволяет отдать предпочтение точности (все условия должны быть выполнены). По умолчанию используется `searchMode=any`, что может порождать неожиданные результаты, если вы формируете запрос с несколькими операторами и получаете больше результатов, а не сокращаете их число до более точных. Это особенно верно для оператора NOT, когда результаты включают в себя все документы, которые "не содержат" конкретный термин.

С помощью параметра searchMode по умолчанию (any) возвращаются 2800 документов. Это документы, которые содержат составной термин "fire department", а также все документы, в которых нет термина "Metrotech Center".

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  :::image type="content" source="media/search-query-simple-examples/searchmodeany.png" alt-text="режим поиска &quot;любой&quot;" border="false":::

Изменение значения параметра searchMode на `all` обеспечивает эффект накопления условий и возвращает меньший результирующий набор — 21 документ. Это документы, содержащие фразу ""fire department", кроме вакансий по адресу Metrotech Center.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  :::image type="content" source="media/search-query-simple-examples/searchmodeall.png" alt-text="режим поиска &quot;все&quot;" border="false":::

## <a name="example-8-structuring-results"></a>Пример 8. Структурирование результатов

Несколько параметров управляют тем, какие поля добавляются в результаты поиска, числом документов, возвращаемых в каждом пакете, и порядком сортировки. В этом примере используются некоторые из предыдущих примеров, но результаты ограничиваются конкретными полями с помощью оператора **$select** и условия буквального поиска. Результат содержит 82 совпадения. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Они добавлены к предыдущему примеру, и их можно отсортировать по заголовку. Сортировка работает, так как поле civil_service_title является *сортируемым* в индексе.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Разбиение результатов по страницам реализуется с помощью параметра **$top**. В этом случае возвращаются первые 5 документов.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Чтобы получить следующие 5 документов, пропустите первый пакет.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Следующие шаги
Попробуйте указать запросы в коде. Чтобы узнать о том, как настроить поисковые запросы для .NET и REST API, используя простой синтаксис по умолчанию, воспользуйтесь приведенными ссылками.

* [Запрос индекса с помощью пакета SDK для .NET](./search-get-started-dotnet.md)
* [Запрос индекса с помощью REST API](./search-get-started-powershell.md)

Дополнительные справочные материалы по синтаксису, архитектуре запросов и примеры можно найти по следующим ссылкам:

+ [Примеры синтаксиса запросов Lucene для создания расширенных запросов](search-query-lucene-examples.md)
+ [How full text search works in Azure Cognitive Search](search-lucene-query-architecture.md) (Как выполняется полнотекстовый поиск в Когнитивном поиске Azure)
+ [Простой синтаксис запросов](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Полный запрос Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Фильтры и синтаксис Orderby](/rest/api/searchservice/odata-expression-syntax-for-azure-search)