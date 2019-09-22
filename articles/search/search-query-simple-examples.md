---
title: Создание простого запроса с помощью службы поиска Azure
description: Изучите пример, запустив запросы на основе простого синтаксиса полнотекстового поиска, фильтра поиска, географического поиска и поиска по индексу поиска Azure.
author: HeidiSteen
manager: nitinme
tags: Simple query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 7c4aeef07d34159e01f188effae77926895e2857
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2019
ms.locfileid: "71179188"
---
# <a name="create-a-simple-query-in-azure-search"></a>Создание простого запроса в службе поиска Azure

В поиске Azure [простой синтаксис запроса](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) вызывает средство синтаксического анализа запросов по умолчанию для выполнения запросов полнотекстового поиска по индексу. Это средство синтаксического анализа работает быстро и обрабатывает распространенные сценарии, включая полнотекстовый поиск, фильтрованный и аспектный Поиск, а также географический поиск. 

В этой статье мы будем использовать примеры для иллюстрации простого синтаксиса.

Альтернативный синтаксис запросов — это [полная Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), поддерживающая более сложные структуры запросов, например поиск нечетких и подстановочных знаков, для обработки которых может потребоваться дополнительное время. Дополнительные сведения и примеры, демонстрирующие полный синтаксис, см. [в разделе Использование полного синтаксиса Lucene](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>Формирование запросов в Postman

В следующих примерах используется индекс поиска вакансий в Нью-Йорке, состоящий из вакансий, доступных на основе набора данных, полученных в рамках инициативы [City of New York OpenData](https://nycopendata.socrata.com/). Эти данные не являются текущими или завершенными. Индекс размещен в службе песочницы корпорации Майкрософт. Это означает, что для использования этих запросов подписка Azure или служба поиска Azure не требуется.

Вам потребуется Postman или аналогичный инструмент для отправки HTTP-запроса GET. Дополнительные сведения см. в [кратком руководстве Изучите службу поиска Azure REST API](search-get-started-postman.md)используя POST.

### <a name="set-the-request-header"></a>Настройка заголовка запроса

1. В заголовке запроса задайте значение **Content-Type** для `application/json`.

2. Добавьте элемент **api-key** и задайте для него следующую строку: `252044BE3886FE4A8E3BAA4F595114BB`. Это ключ запроса для службы поиска в песочнице, в которой размещен индекс поиска вакансий Нью-Йорка.

Настроенный заголовок запроса можно многократно использовать для всех запросов в этой статье, только заменяя строку **search=** . 

  ![Заголовок запроса Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Настройка URL-адреса запроса

Запрос представляет собой команду GET и URL-адрес, содержащий строку конечную точку Поиска Azure и строку поиска.

  ![Заголовок запроса Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

URL-адрес содержит следующие элементы.

+ **`https://azs-playground.search.windows.net/`** — служба поиска в песочнице, обслуживаемая командой разработчиков Поиска Azure. 
+ **`indexes/nycjobs/`** — это индекс вакансий Нью-Йорка в коллекции индексов этой службы. Имя службы и индекс должны быть указаны в запросе.
+ **`docs`** — коллекция документов, содержащая все содержимое для поиска. Значение api-key в заголовке запроса подходит только для операций чтения, нацеленных на эту коллекцию документов.
+ **`api-version=2019-05-06`** задает значение api-version. Это обязательный параметр в каждом запросе.
+ **`search=*`** —это строка запроса, которая имеет значение NULL в исходном запросе и возвращает первые 50 результатов (по умолчанию).

## <a name="send-your-first-query"></a>Отправка первого запроса

В качестве шага проверки вставьте приведенный ниже запрос в раздел GET и щелкните **Отправить**. Будут возвращены результаты в виде подробных документов JSON. Возвращаются все документы, что позволяет просматривать все поля и все значения.

Вставьте этот URL-адрес в клиент RESTFUL в качестве шага проверки и просмотрите структуру документа.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=*
  ```

Строка запроса **`search=*`** эквивалентна неточному поиску значения NULL или пустого значения. Этот запрос не очень полезен, но это самый простой поиск, который можно выполнить.

При необходимости можно добавить **`$count=true`** в URL-адрес, чтобы вернуть количество документов, соответствующих условиям поиска. При пустой строке поиска это будут все документы в индексе (примерно 2800 в случае вакансий в Нью-Йорке).

## <a name="how-to-invoke-simple-query-parsing"></a>Как вызвать анализ простого запроса

Для интерактивных запросов не нужно указывать дополнительные параметры: простой синтаксис используется по умолчанию. В коде, если вы ранее вызывали **queryType=full** для применения полного синтаксиса запросов, можно вернуть значение по умолчанию, **queryType=simple**.

## <a name="example-1-field-scoped-query"></a>Пример 1 Запрос по полям

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
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=business_title&$select=business_title&search=*
```

Результат запроса должен выглядеть, как на снимке экрана ниже.

  ![Пример ответа Postman](media/search-query-lucene-examples/postman-sample-results.png)

Вы могли заметить оценку поиска в ответе. Универсальная оценка 1 отображается, если приоритет не указан, потому что выполнен поиск не всего текста или не указано условие. Для нулевого поиска без критериев строки возвращаются в произвольном порядке. Когда вы добавите условие поиска, вы увидите, как оценки поиска превратятся в понятные значения.

## <a name="example-2-look-up-by-id"></a>Пример 2 Поиск по идентификатору

Это немного нетипичный пример. Однако при оценке поведения при поиске может потребоваться проверить все содержимое конкретного документа, чтобы понять, почему он был включен в результаты или исключен из них. Чтобы вернуть весь документ, используйте [операцию поиска](https://docs.microsoft.com/rest/api/searchservice/lookup-document) для передачи идентификатора документа.

Все документы имеют уникальный идентификатор. Чтобы проверить синтаксис запроса поиска, сначала получите список идентификаторов документов, чтобы найти в нем нужный идентификатор. В индексе вакансий в Нью-Йорке идентификаторы хранятся в поле `id`.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=id&$select=id&search=*
```

В следующем примере выполняется запрос поиска, возвращающий определенный документ по значению `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", которое было указано первым в предыдущем ответе. Следующий запрос возвращает весь документ, а не отдельные поля. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2019-05-06&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>Пример 3 Фильтрация запросов

[Синтаксис фильтра](https://docs.microsoft.com/azure/search/search-query-odata-filter) — это выражение OData, которое можно использовать с параметром **search** или само по себе. Автономный фильтр без параметра поиска полезен, когда выражение фильтра может полностью определить интересующие документы. Без строки запроса не выполняется лексический или лингвистический анализ (все оценки имеют значение 1), нет оценки и рейтинга. Обратите внимание, что строка поиска пуста.

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "select=job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Если они используются вместе, сначала ко всему индексу применяется фильтр, а затем в результатах фильтрации выполняется поиск. Поэтому фильтры могут повысить производительность запросов, так как они уменьшают количество документов, которые поисковый запрос должен обработать.

  ![Фильтр ответа на запрос](media/search-query-simple-examples/filtered-query.png)

Если вы хотите попробовать это в Postman, используя GET, вы можете вставить эту строку:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

Еще один эффективный способ комбинировать фильтр и поиск — использовать **`search.ismatch*()`** в выражении фильтра, что позволяет добавить поисковый запрос в фильтр. Это выражение фильтра использует подстановочный знак для термина *план*, чтобы выбрать должность business_title, содержащую термины "план", "планировщик", "планирование" и т. д.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

Дополнительные сведения о функции см. в [описании search.ismatch в разделе с примерами фильтров](https://docs.microsoft.com/azure/search/search-query-odata-full-text-search-functions#examples).

## <a name="example-4-range-filters"></a>Пример 4. Фильтры диапазонов

Фильтрация диапазона поддерживается с помощью выражения **`$filter`** для любого типа данных. В следующих примерах выполняется поиск по числовым и строковым полям. 

Типы данных важны в фильтрах диапазона и лучше всего работают, когда числовые данные находятся в числовых полях, а строковые данные — в строковых. Числовые данные в строковых полях не подходят для диапазонов, потому что числовые строки не сопоставимы в службе "Поиск Azure". 

Следующие примеры приведены в формате POST для удобства чтения (числовой диапазон, за которым следует текстовый диапазон):

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency",
      "count": "true"
    }
```
  ![Фильтр диапазона для числовых диапазонов](media/search-query-simple-examples/rangefilternumeric.png)


```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  ![Фильтр диапазона для текстовых диапазонов](media/search-query-simple-examples/rangefiltertext.png)

Вы также можете попробовать их в Postman, используя GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> В приложениях поиска часто используется фасетная навигация на основе диапазонов значений. Дополнительные сведения и примеры создания фильтров для структур фасетной навигации см. в разделе ["Фильтрация по диапазону значений" в статье *Как реализовать фасетную навигацию в службе поиска Azure*](search-faceted-navigation.md#filter-based-on-a-range).

## <a name="example-5-geo-search"></a>Пример 5. Геопространственный поиск

Индекс выборки включает в себя поле geo_location с координатами широты и долготы. В этом примере используется [функция geo.distance](https://docs.microsoft.com/azure/search/search-query-odata-geo-spatial-functions#examples), которая фильтрует документы в пределах окружности начальной точки до произвольного расстояния (в километрах), которое вы предоставляете. Вы можете отрегулировать последнее значение в запросе (4), чтобы уменьшить или увеличить площадь поверхности запроса.

Следующий пример приведен в формате POST для удобства чтения:

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
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
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>Пример 6. Точность поиска

Запросы терминов позволяют искать одиночные термины или наборы терминов, которые оцениваются независимо друг от друга. Запросы фраз заключаются кавычки и проверяются как буквальная строка. Точностью соответствия управляют операторы и параметр searchMode.

Пример 1. **`&search=fire`** возвращает 150 результатов, в которых все найденные документы содержат слово "fire".

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire
```

Пример 2. **`&search=fire department`** возвращает 2002 результата. Возвращаются документы, содержащие слово "fire" или "department".

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire department
```

Пример 3. **`&search="fire department"`** возвращает 82 результата. Заключение строки в кавычки позволяет выполнить буквальный поиск обоих терминов, и в индексе, состоящем из объединенных терминов, найдены совпадения с терминами, снабженными маркером. Это объясняет, почему поисковый запрос **`search=+fire +department`** не является эквивалентным. Оба термина являются обязательными, но их наличие проверяется независимо друг от друга. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>Пример 7. Логические операторы с параметром searchMode

Простой синтаксис поддерживает логические операторы в виде знаков (`+, -, |`). Параметр searchMode задает компромисс между точностью и полнотой результатов. `searchMode=any` позволяет отдать предпочтение полноте (в результирующий набор добавляется любой документ, соответствующий любому заданному условию), а `searchMode=all` позволяет отдать предпочтение точности (все условия должны быть выполнены). По умолчанию используется `searchMode=any`, что может порождать неожиданные результаты, если вы формируете запрос с несколькими операторами и получаете больше результатов, а не сокращаете их число до более точных. Это особенно верно для оператора NOT, когда результаты включают в себя все документы, которые "не содержат" конкретный термин.

С помощью параметра searchMode по умолчанию (any) возвращаются 2800 документов. Это документы, которые содержат составной термин "fire department", а также все документы, в которых нет термина "Metrotech Center".

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  ![режим поиска "любой"](media/search-query-simple-examples/searchmodeany.png)

Изменение значения параметра searchMode на `all` обеспечивает эффект накопления условий и возвращает меньший результирующий набор — 21 документ. Это документы, содержащие фразу ""fire department", кроме вакансий по адресу Metrotech Center.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  ![режим поиска "все"](media/search-query-simple-examples/searchmodeall.png)

## <a name="example-8-structuring-results"></a>Пример 8. Структурирование результатов

Несколько параметров управляют тем, какие поля добавляются в результаты поиска, числом документов, возвращаемых в каждом пакете, и порядком сортировки. В этом примере используются некоторые из предыдущих примеров, но результаты ограничиваются конкретными полями с помощью оператора **$select** и условия буквального поиска. Результат содержит 82 совпадения. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Они добавлены к предыдущему примеру, и их можно отсортировать по заголовку. Сортировка работает, так как поле civil_service_title является *сортируемым* в индексе.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Разбиение результатов по страницам реализуется с помощью параметра **$top**. В этом случае возвращаются первые 5 документов.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Чтобы получить следующие 5 документов, пропустите первый пакет.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Следующие шаги
Попробуйте указать запросы в коде. Чтобы узнать о том, как настроить поисковые запросы для .NET и REST API, используя простой синтаксис по умолчанию, воспользуйтесь приведенными ссылками.

* [Отправка запросов в индекс службы поиска Azure с помощью пакета SDK для .NET](search-query-dotnet.md)
* [Отправка запросов в индекс службы поиска Azure с помощью REST API](search-create-index-rest-api.md)

Дополнительные справочные материалы по синтаксису, архитектуре запросов и примеры можно найти по следующим ссылкам:

+ [Примеры синтаксиса запросов Lucene для создания расширенных запросов](search-query-lucene-examples.md)
+ [Как работает полнотекстовый поиск в службе поиска Azure](search-lucene-query-architecture.md)
+ [Синтаксис простых запросов](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Полный запрос Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Фильтры и синтаксис Orderby](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)
