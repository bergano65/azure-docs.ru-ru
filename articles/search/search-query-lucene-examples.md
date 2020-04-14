---
title: Используйте полный синтаксис запроса Lucene
titleSuffix: Azure Cognitive Search
description: Синтаксис lucene запроса для нечеткого поиска, поиска близости, повышения терминов, регулярного поиска выражений и поиска подстановочных знаков в службе когнитивного поиска Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bc691299f38d562aee5c08a89e10372331663f8e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262814"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Используйте "полный" синтаксис поиска Lucene (продвинутые запросы в Azure Cognitive Search)

При построении запросов для Azure Cognitive Search можно заменить [простой курсер запроса](query-simple-syntax.md) по умолчанию более обширным [Lucene query Parser в Azure Cognitive Search](query-lucene-syntax.md) для формулирования специализированных и продвинутых определений запросов. 

Parser Lucene поддерживает сложные конструкции запросов, такие как запросы на поле, нечеткий поиск, поиск подстановочных знаков infix и суффикса, поиск близости, ускорение терминов и регулярный поиск выражения. Дополнительные возможности поставляются с дополнительными требованиями к обработке, поэтому следует предусмотреть немного больше времени для выполнения. В этой статье можно пошагово выполнить примеры полного синтаксиса операций запросов.

> [!Note]
> Многие специализированные конструкции запросов, обеспечиваемые благодаря полному синтаксису запросов Lucene, не поддерживают [анализ текста](search-lucene-query-architecture.md#stage-2-lexical-analysis), что может оказаться неожиданным, если вы хотите использовать выделение корней слов или лемматизацию. Лексический анализ выполняется только для полными терминами (запрос термина или запрос фразы). Типы запросов с неполными терминами (запрос с префиксом, запрос с подстановочными знаками, запрос с регулярными выражениями, нечеткий запрос) добавляются непосредственно к дереву запроса в обход этапа анализа. Единственным преобразованием, выполняемым на условиях частичного запроса, является снижение уровня. 
>

## <a name="formulate-requests-in-postman"></a>Формирование запросов в Postman

В следующих примерах используется индекс поиска вакансий в Нью-Йорке, состоящий из вакансий, доступных на основе набора данных, полученных в рамках инициативы [City of New York OpenData](https://opendata.cityofnewyork.us/). Эти данные не являются текущими или завершенными. Индекс находится на службе песочницы, предоставляемой корпорацией Майкрософт, что означает, что для просмотра этих запросов не требуется подписка Azure или Azure Cognitive Search.

Вам потребуется Postman или аналогичный инструмент для отправки HTTP-запроса GET. Дополнительные сведения см. в статье [Работа с REST API службы "Поиск Azure" с помощью Fiddler или Postman](search-get-started-postman.md).

### <a name="set-the-request-header"></a>Настройка заголовка запроса

1. В заголовке запроса задайте значение **Content-Type** для `application/json`.

2. Добавьте элемент **api-key** и задайте для него следующую строку: `252044BE3886FE4A8E3BAA4F595114BB`. Это ключ запроса для службы поиска в песочнице, в которой размещен индекс поиска вакансий Нью-Йорка.

Настроенный заголовок запроса можно многократно использовать для всех запросов в этой статье, только заменяя строку **search=**. 

  ![Заголовок запроса Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Настройка URL-адреса запроса

Запрос — это команда GET в паре с URL-адресом, содержащим конечную точку Azure Cognitive Search и строку поиска.

  ![Заголовок запроса Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

URL-адрес содержит следующие элементы.

+ **`https://azs-playground.search.windows.net/`**— служба поиска в песочнице, поддерживаемая группой разработчиков Azure Cognitive Search. 
+ **`indexes/nycjobs/`** является индексом NYC Jobs в сборе индексов этой услуги. Имя службы и индекс должны быть указаны в запросе.
+ **`docs`** представляет собой сбор документов, содержащий все содержимое, содержащее сяпоните. Значение api-key в заголовке запроса подходит только для операций чтения, нацеленных на эту коллекцию документов.
+ **`api-version=2019-05-06`** устанавливает api-версию, которая является необходимым параметром по каждому запросу.
+ **`search=*`** строка запроса, которая в первоначальном запросе является нулевой, возвращая первые 50 результатов (по умолчанию).

## <a name="send-your-first-query"></a>Отправка первого запроса

В качестве шага проверки вставьте приведенный ниже запрос в раздел GET и щелкните **Отправить**. Будут возвращены результаты в виде подробных документов JSON. Возвращаются целые документы, что позволяет увидеть все поля и все значения.

Вставьте этот URL в клиент REST в качестве шага проверки и для просмотра структуры документов.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=*
  ```

Строка запроса, **`search=*`** является неопределенным поиском, эквивалентным нулевым или пустым поиском. Это самый простой поиск вы можете сделать.

Дополнительно можно добавить **`$count=true`** в URL-адрес, чтобы вернуть подсчет документов, соответствующих критериям поиска. При пустой строке поиска это будут все документы в индексе (примерно 2800 в случае вакансий в Нью-Йорке).

## <a name="how-to-invoke-full-lucene-parsing"></a>Способы вызова полного синтаксического анализа Lucene

Добавьте **queryType=full**, чтобы вызвать полный синтаксис запроса, переопределив простой синтаксис запроса по умолчанию. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&search=*
```

Во всех примерах в этой статье задается параметр поиска **queryType=full**, указывающий, что полный синтаксис обрабатывается средством синтаксического анализа запросов Lucene. 

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Пример 1: Запрос, примкнет к списку полей

Этот первый пример не специфичен для Lucene, но мы ведем с ним введение первой фундаментальной концепции запроса: область поля. Этот пример прививает весь запрос и ответ только на несколько конкретных полей. Знать, как структурировать читаемый ответ JSON, важно, если используется инструмент Postman или обозреватель поиска. 

Для краткости запрос нацелен только на поле *business_title* и указывает возвращаемые должности. Параметр **поискаFields** ограничивает выполнение запроса только полем business_title и **выбирает,** какие поля включены в ответ.

### <a name="search-expression"></a>Выражение поиска

```http
&search=*&searchFields=business_title&$select=business_title
```

Вот один и тот же запрос с несколькими полями в списке запятой.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

Пространства после запятых не являются обязательными.

> [!Tip]
> При использовании REST API из кода приложения не забывайте кодировать `$select` параметры, такие как и под кодируйте URL-коды, как и `searchFields`.

### <a name="full-url"></a>Полный URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

Результат запроса должен выглядеть, как на снимке экрана ниже.

  ![Пример ответа Postman](media/search-query-lucene-examples/postman-sample-results.png)

Вы могли заметить оценку поиска в ответе. Универсальная оценка 1 отображается, если приоритет не указан, потому что выполнен поиск не всего текста или не указано условие. Для нулевого поиска без критериев строки возвращаются в произвольном порядке. Когда вы включаете фактические критерии поиска, вы увидите, как оценки поиска превращаются в значимые значения.

## <a name="example-2-fielded-search"></a>Пример 2: Полевой поиск

Полный синтаксис Lucene поддерживает отслеживание индивидуальных выражений поиска в определенном поле. Этот пример ищет названия дела с термином старший в их, но не младший.

### <a name="search-expression"></a>Выражение поиска

```http
$select=business_title&search=business_title:(senior NOT junior)
```

Вот один и тот же запрос с несколькими полями.

```http
$select=business_title, posting_type&search=business_title:(senior NOT junior) AND posting_type:external
```

### <a name="full-url"></a>Полный URL

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&$select=business_title&search=business_title:(senior NOT junior)
```

  ![Пример ответа Postman](media/search-query-lucene-examples/intrafieldfilter.png)

Вы можете определить полевую операцию поиска с помощью синтаксиса **fieldName:searchExpression,** где выражение поиска может быть одним словом или фразой, или более сложным выражением в скобках, по желанию с операторами Boolean. Вот несколько примеров.

- `business_title:(senior NOT junior)`
- `state:("New York" OR "New Jersey")`
- `business_title:(senior NOT junior) AND posting_type:external`

Не забудьте поместить несколько строк в кавычки, если вы хотите, чтобы обе строки были `state` оценены как единое целое, как в данном случае поиск двух различных местоположений в поле. Кроме того, оператор должен быть указан в верхнем регистре, как в случае с NOT и AND.

Поле, указанное в **fieldName:searchExpression** должно быть полем поиска. Подробную информацию о том, как атрибуты индекса используются в определениях поля, можно найти [индекс «Создай индекс» (Azure Cognitive Search REST API).](https://docs.microsoft.com/rest/api/searchservice/create-index)

> [!NOTE]
> В приведенном выше примере нам `searchFields` не нужно было использовать параметр, поскольку каждая часть запроса имеет явно указанное имя поля. Тем не менее, `searchFields` вы все равно можете использовать параметр, если вы хотите запустить запрос, в котором некоторые части относятся к определенному полю, а остальные могут применяться к нескольким полям. Например, `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` запрос будет `senior NOT junior` соответствовать `business_title` только полю, в то время `posting_type` как он будет соответствовать "внешнему" с полем. Имя поля, представленное в **fieldName:searchExpression** `searchFields` всегда имеет приоритет над параметром, поэтому в `business_title` этом `searchFields` примере нам не нужно включать в параметр.

## <a name="example-3-fuzzy-search"></a>Пример 3. Поиск нечетких соответствий

Полный синтаксис Lucene также поддерживает поиск нечетких соответствий, то есть сопоставление терминов с подобной конструкцией. Чтобы выполнить поиск нечетких соответствий, необходимо добавить символ тильды `~` в конце слова. Дополнительно можно поставить цифру от 0 до 2, указывающую расстояние редактирования. Например, `blue~` или `blue~1` вернет результаты с blue, blues и glue.

### <a name="search-expression"></a>Выражение поиска

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

Фразы не поддерживаются напрямую, но можно указать нечеткое соответствие по составным частям фразы.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>Полный URL

Этот запрос осуществляет поиск вакансий со словом "associate" (намеренно написанного с ошибкой).

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![Ответ на поиск нечетких соответствий](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> Запросы с нечетким соответствием не [анализируются](search-lucene-query-architecture.md#stage-2-lexical-analysis). Типы запросов с неполными терминами (запрос с префиксом, запрос с подстановочными знаками, запрос с регулярными выражениями, нечеткий запрос) добавляются непосредственно к дереву запроса в обход этапа анализа. Единственное преобразование для неполных терминов запроса — преобразование в нижний регистр.
>

## <a name="example-4-proximity-search"></a>Пример 4. Поиск с учетом расположения
Операция поиска с учетом расположения позволяет найти слова, расположенные рядом в документе. Вставьте символ тильды "~" в конце фразы, а затем — цифру, обозначающую количество слов, определяющее границу близости. Например, если ввести "hotel airport"~5, будут найдены слова "hotel" и "airport", расположенные в пределах 5 слов друг от друга в документе.

### <a name="search-expression"></a>Выражение поиска

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>Полный URL

Этот запрос осуществляет поиск вакансий, содержащих словосочетание "senior analyst", разделенное не более чем одним словом.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  ![Запрос с учетом расположения](media/search-query-lucene-examples/proximity-before.png)

Выполните эту операцию поиска снова, при этом не допуская разделения словосочетания "senior analyst" другими словами. Обратите внимание на то, что для этого запроса возвращены 8 документов, в отличие от 10 документов для предыдущего запроса.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Пример 5. Повышение приоритета терминов
При повышении приоритета слов документы сортируются по приоритету, т. е. документы, в которых содержится условие поиска, имеют высший приоритет по отношению к документам, в которых его нет. Чтобы повысить приоритет условия поиска, используйте символ крышки "^" и коэффициент повышения приоритета (число) в конце этого условия. 

### <a name="full-urls"></a>Полные URL-адреса

Этот запрос before выполняет поиск вакансий, содержащих термин *computer analyst*. Обратите внимание на то, что вакансии, содержащие оба слова *computer* и *analyst*, не найдены, а вакансии со словом *computer* выведены в верхней части результатов.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  ![Повышение приоритета термина before](media/search-query-lucene-examples/termboostingbefore.png)

В запросе after выполните этот поиск снова, при этом повышая приоритет результатов, содержащих термин *analyst*, относительно результатов, содержащих слово *computer*, если оба слова не встречаются одновременно. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
Более понятная для человека версия приведенного выше запроса: `search=business_title:computer analyst^2`. Для работоспособности запроса `^2` кодируется как `%5E2`, что сложнее для чтения.

  ![Повышение приоритета термина after](media/search-query-lucene-examples/termboostingafter.png)

Повышение приоритета терминов отличается от профилей повышения, так как они повышают приоритет определенных полей, а не определенных терминов. В следующем примере показаны эти различия.

Рассмотрим профиль повышения, который повышает приоритет совпадений в определенном поле, таком как **genre** в примере musicstoreindex. Повышение значимости слов может использоваться для дальнейшего повышения приоритета определенных условий поиска относительно других. Например, при вводе запроса "rock^2 electronic" документы, содержащие это условие поиска в поле **genre** , становятся приоритетнее документов, содержащих это условие поиска в других полях, поддерживающих поиск, в индексе. Кроме того, документы, содержащие слово "rock", будут иметь более высокий приоритет, чем документы, содержащие слово "electronic", так как введен коэффициент повышения приоритета (2).

Чем выше коэффициент повышения приоритета при настройке коэффициента, тем приоритетнее условие поиска относительно других. Коэффициент повышения приоритета по умолчанию — 1. Несмотря на то, что коэффициент повышения приоритета должен быть положительным числом, он может быть меньше 1 (например, 0,2).


## <a name="example-6-regex"></a>Пример 6. Регулярное выражение

Операция поиска по регулярным выражениям позволяет найти совпадение в зависимости от содержимого между косыми чертами "/", как указано в документации [класса RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

### <a name="search-expression"></a>Выражение поиска

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>Полный URL

В этом запросе, поиск рабочих мест либо `search=business_title:/(Sen|Jun)ior/`с термином Старший или младший: .

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  ![Запрос регулярных выражений](media/search-query-lucene-examples/regex.png)

> [!Note]
> Запросы с регулярными выражениями не [анализируются](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). Единственное преобразование для неполных терминов запроса — преобразование в нижний регистр.
>

## <a name="example-7-wildcard-search"></a>Пример 7. Поиск с использованием подстановочных знаков
Вы можете использовать распознаваемый синтаксис для поиска с использованием одного (?) или нескольких (\*) подстановочных знаков. Обратите внимание, что средство синтаксического анализа запросов Lucene поддерживает использование этих символов для поиска одного слова, а не фразы.

### <a name="search-expression"></a>Выражение поиска

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>Полный URL

Этот запрос выполняет поиск вакансий, содержащих в начале"prog". В результате будут найдены вакансии со словами "programming" и "programmer". Символ "*" или "?" не может находиться в начале поискового запроса.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  ![Запрос с подстановочным знаком](media/search-query-lucene-examples/wildcard.png)

> [!Note]
> Запросы с подстановочными знаками не [анализируются](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). Единственное преобразование для неполных терминов запроса — преобразование в нижний регистр.
>

## <a name="next-steps"></a>Дальнейшие действия
Попробуйте указать средство синтаксического анализа запросов Lucene в коде. Чтобы узнать о том, как настроить запросы поиска для .NET и REST API, воспользуйтесь приведенными ссылками. В этих ссылках используется простой синтаксис по умолчанию, поэтому потребуется применить полученные из этой статьи знания, чтобы задать **queryType**.

* [Запрос индекса с помощью .NET SDK](search-query-dotnet.md)
* [Запрос индекса с помощью REST API](search-create-index-rest-api.md)

Дополнительные справочные материалы по синтаксису, архитектуре запросов и примеры можно найти по следующим ссылкам:

+ [Примеры запросов с использованием простого синтаксиса](search-query-simple-examples.md)
+ [How full text search works in Azure Cognitive Search](search-lucene-query-architecture.md) (Как выполняется полнотекстовый поиск в Когнитивном поиске Azure)
+ [Простой синтаксис запросов](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Полный синтаксис запросов Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)