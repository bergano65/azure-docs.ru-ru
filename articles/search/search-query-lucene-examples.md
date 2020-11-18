---
title: Использовать полный синтаксис запроса Lucene
titleSuffix: Azure Cognitive Search
description: Синтаксис запроса Lucene для поиска нечетких результатов, поиска с учетом расположения, повышения терминов, поиска регулярных выражений и поиска с подстановочными знаками в службе Когнитивный поиск Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: ae4dd8b82e40b46da52a1b1f396569fda1dfea2b
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94694632"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Использование полного синтаксиса поиска Lucene (расширенные запросы в Azure Когнитивный поиск)

При создании запросов для Когнитивный поиск Azure вы можете заменить средство [синтаксического анализа простых запросов](query-simple-syntax.md) по умолчанию на более обширное [средство синтаксического анализа запросов Lucene в когнитивный Поиск Azure](query-lucene-syntax.md) , чтобы сформулировать специализированные и расширенные определения запросов. 

Средство синтаксического анализа Lucene поддерживает сложные конструкции запросов, такие как запросы с областью видимости полей, Поиск нечетких запросов, инфиксные и подстановочный знак суффикса, поиск по сходству, повышение термина и поиск регулярных выражений. Дополнительные возможности поставляются с дополнительными требованиями к обработке, поэтому следует предусмотреть немного больше времени для выполнения. В этой статье можно пошагово выполнить примеры полного синтаксиса операций запросов.

> [!Note]
> Многие специализированные конструкции запросов, обеспечиваемые благодаря полному синтаксису запросов Lucene, не поддерживают [анализ текста](search-lucene-query-architecture.md#stage-2-lexical-analysis), что может оказаться неожиданным, если вы хотите использовать выделение корней слов или лемматизацию. Лексический анализ выполняется только для полными терминами (запрос термина или запрос фразы). Типы запросов с неполными терминами (запрос с префиксом, запрос с подстановочными знаками, запрос с регулярными выражениями, нечеткий запрос) добавляются непосредственно к дереву запроса в обход этапа анализа. Единственным преобразованием, выполняемым в условиях частичного запроса, является нижний регистр. 
>

## <a name="formulate-requests-in-postman"></a>Формирование запросов в Postman

В следующих примерах используется индекс поиска вакансий в Нью-Йорке, состоящий из вакансий, доступных на основе набора данных, полученных в рамках инициативы [City of New York OpenData](https://opendata.cityofnewyork.us/). Эти данные не являются текущими или завершенными. Индекс находится в службе "песочницы", предоставляемой корпорацией Майкрософт. Это означает, что для работы с этими запросами не требуется подписка Azure или Azure Когнитивный поиск.

Вам потребуется Postman или аналогичный инструмент для отправки HTTP-запроса GET. Дополнительные сведения см. в статье [Работа с REST API службы "Поиск Azure" с помощью Fiddler или Postman](search-get-started-rest.md).

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

Строка запроса, **`search=*`** , является неопределенным поиском, эквивалентом null или пустым поиском. Это самый простой поиск, который можно выполнить.

При необходимости можно добавить **`$count=true`** в URL-адрес, чтобы вернуть число документов, соответствующих условиям поиска. При пустой строке поиска это будут все документы в индексе (примерно 2800 в случае вакансий в Нью-Йорке).

## <a name="how-to-invoke-full-lucene-parsing"></a>Способы вызова полного синтаксического анализа Lucene

Добавьте **queryType=full**, чтобы вызвать полный синтаксис запроса, переопределив простой синтаксис запроса по умолчанию. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&search=*
```

Во всех примерах в этой статье задается параметр поиска **queryType=full**, указывающий, что полный синтаксис обрабатывается средством синтаксического анализа запросов Lucene. 

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Пример 1. запрос с областью действия списка полей

Первый пример не зависит от Lucene, но мы создаем его, чтобы ввести первую фундаментальную концепцию запроса: область поля. В этом примере областью действия является весь запрос и ответ только на несколько конкретных полей. Знать, как структурировать читаемый ответ JSON, важно, если используется инструмент Postman или обозреватель поиска. 

Для краткости запрос нацелен только на поле *business_title* и указывает возвращаемые должности. Параметр **searchFields** позволяет ограничить выполнение запроса только business_title полем, а **SELECT** указывает, какие поля включены в ответ.

### <a name="search-expression"></a>Выражение поиска

```http
&search=*&searchFields=business_title&$select=business_title
```

Ниже приведен тот же запрос с несколькими полями в списке, разделенном запятыми.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

Пробелы после запятых необязательны.

> [!Tip]
> При использовании REST API из кода приложения не забудьте указать параметры кодирования URL-адреса, такие как `$select` и `searchFields` .

### <a name="full-url"></a>Полный URL-адрес

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

Результат запроса должен выглядеть, как на снимке экрана ниже.

  ![Публикация образца ответа с результатами](media/search-query-lucene-examples/postman-sample-results.png)

Вы могли заметить оценку поиска в ответе. Универсальная оценка 1 отображается, если приоритет не указан, потому что выполнен поиск не всего текста или не указано условие. Для нулевого поиска без критериев строки возвращаются в произвольном порядке. При включении фактических условий поиска результаты поиска будут развиваться в осмысленных значениях.

## <a name="example-2-fielded-search"></a>Пример 2. Поиск по полям

Полный синтаксис Lucene поддерживает определение области отдельных выражений поиска для определенного поля. В этом примере выполняется поиск названий предприятий с термином старший в них, но не младшим.

### <a name="search-expression"></a>Выражение поиска

```http
$select=business_title&search=business_title:(senior NOT junior)
```

Ниже приведен тот же запрос с несколькими полями.

```http
$select=business_title, posting_type&search=business_title:(senior NOT junior) AND posting_type:external
```

### <a name="full-url"></a>Полный URL-адрес

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&$select=business_title&search=business_title:(senior NOT junior)
```

  :::image type="content" source="media/search-query-lucene-examples/intrafieldfilter.png" alt-text="Пример выражения поиска ответа после примера" border="false":::

Можно определить операцию поиска по полю с помощью синтаксиса **fieldname: сеарчекспрессион** , где выражение поиска может представлять собой одно слово или фразу или более сложное выражение в круглых скобках, при необходимости с логическими операторами. Вот несколько примеров.

- `business_title:(senior NOT junior)`
- `state:("New York" OR "New Jersey")`
- `business_title:(senior NOT junior) AND posting_type:external`

Если требуется, чтобы обе строки вычислить как единую сущность, необходимо поместить несколько строк в кавычки, как в этом случае искать два различных расположения в `state` поле. Кроме того, оператор должен быть указан в верхнем регистре, как в случае с NOT и AND.

Поле, указанное в **fieldname: сеарчекспрессион** , должно быть полем с возможностью поиска. Дополнительные сведения об использовании атрибутов индекса в определениях полей см. в статье [Создание индекса (REST API когнитивный Поиск Azure)](/rest/api/searchservice/create-index) .

> [!NOTE]
> В приведенном выше примере не нужно использовать `searchFields` параметр, так как в каждой части запроса указано явное имя поля. Тем не менее можно по-прежнему использовать `searchFields` параметр, если требуется выполнить запрос, в котором часть частей ограничена определенным полем, а остальное может быть применено к нескольким полям. Например, запрос `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` будет сопоставляться `senior NOT junior` только с `business_title` полем, тогда как он будет сопоставляться с `posting_type` полем external. Имя поля, указанное в **fieldname: сеарчекспрессион** , всегда имеет приоритет над `searchFields` параметром, поэтому в этом примере не нужно включать `business_title` в `searchFields` параметр.

## <a name="example-3-fuzzy-search"></a>Пример 3. Поиск нечетких соответствий

Полный синтаксис Lucene также поддерживает поиск нечетких соответствий, то есть сопоставление терминов с подобной конструкцией. Чтобы выполнить поиск нечетких соответствий, необходимо добавить символ тильды `~` в конце слова. Дополнительно можно поставить цифру от 0 до 2, указывающую расстояние редактирования. Например, `blue~` или `blue~1` вернет результаты с blue, blues и glue.

### <a name="search-expression"></a>Выражение поиска

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

Фразы не поддерживаются напрямую, но можно указать нечеткое соответствие для частей компонентов фразы.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>Полный URL-адрес

Этот запрос осуществляет поиск вакансий со словом "associate" (намеренно написанного с ошибкой).

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
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

### <a name="full-url"></a>Полный URL-адрес

Этот запрос осуществляет поиск вакансий, содержащих словосочетание "senior analyst", разделенное не более чем одним словом.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  :::image type="content" source="media/search-query-lucene-examples/proximity-before.png" alt-text="Запрос с учетом расположения" border="false":::

Выполните эту операцию поиска снова, при этом не допуская разделения словосочетания "senior analyst" другими словами. Обратите внимание на то, что для этого запроса возвращены 8 документов, в отличие от 10 документов для предыдущего запроса.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Пример 5. Повышение приоритета терминов
При повышении приоритета слов документы сортируются по приоритету, т. е. документы, в которых содержится условие поиска, имеют высший приоритет по отношению к документам, в которых его нет. Чтобы повысить приоритет условия поиска, используйте символ крышки "^" и коэффициент повышения приоритета (число) в конце этого условия. 

### <a name="full-urls"></a>Полные URL-адреса

Этот запрос before выполняет поиск вакансий, содержащих термин *computer analyst*. Обратите внимание на то, что вакансии, содержащие оба слова *computer* и *analyst*, не найдены, а вакансии со словом *computer* выведены в верхней части результатов.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  :::image type="content" source="media/search-query-lucene-examples/termboostingbefore.png" alt-text="Повышение приоритета термина before" border="false":::

В запросе after выполните этот поиск снова, при этом повышая приоритет результатов, содержащих термин *analyst*, относительно результатов, содержащих слово *computer*, если оба слова не встречаются одновременно. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
Более понятная для человека версия приведенного выше запроса: `search=business_title:computer analyst^2`. Для работоспособности запроса `^2` кодируется как `%5E2`, что сложнее для чтения.

  :::image type="content" source="media/search-query-lucene-examples/termboostingafter.png" alt-text="Повышение приоритета термина after" border="false":::

Повышение приоритета терминов отличается от профилей повышения, так как они повышают приоритет определенных полей, а не определенных терминов. В следующем примере показаны эти различия.

Рассмотрим профиль повышения, который повышает приоритет совпадений в определенном поле, таком как **genre** в примере musicstoreindex. Повышение значимости слов может использоваться для дальнейшего повышения приоритета определенных условий поиска относительно других. Например, при вводе запроса "rock^2 electronic" документы, содержащие это условие поиска в поле **genre** , становятся приоритетнее документов, содержащих это условие поиска в других полях, поддерживающих поиск, в индексе. Кроме того, документы, содержащие слово "rock", будут иметь более высокий приоритет, чем документы, содержащие слово "electronic", так как введен коэффициент повышения приоритета (2).

Чем выше коэффициент повышения приоритета при настройке коэффициента, тем приоритетнее условие поиска относительно других. Коэффициент повышения приоритета по умолчанию — 1. Несмотря на то, что коэффициент повышения приоритета должен быть положительным числом, он может быть меньше 1 (например, 0,2).


## <a name="example-6-regex"></a>Пример 6. Регулярное выражение

Операция поиска по регулярным выражениям позволяет найти совпадение в зависимости от содержимого между косыми чертами "/", как указано в документации [класса RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

### <a name="search-expression"></a>Выражение поиска

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>Полный URL-адрес

В этом запросе найдите задания с термином старший или младший: `search=business_title:/(Sen|Jun)ior/` .

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  :::image type="content" source="media/search-query-lucene-examples/regex.png" alt-text="Запрос регулярных выражений" border="false":::

> [!Note]
> Запросы с регулярными выражениями не [анализируются](./search-lucene-query-architecture.md#stage-2-lexical-analysis). Единственное преобразование для неполных терминов запроса — преобразование в нижний регистр.
>

## <a name="example-7-wildcard-search"></a>Пример 7. Поиск с использованием подстановочных знаков
Вы можете использовать распознаваемый синтаксис для поиска с использованием одного (?) или нескольких (\*) подстановочных знаков. Обратите внимание, что средство синтаксического анализа запросов Lucene поддерживает использование этих символов для поиска одного слова, а не фразы.

### <a name="search-expression"></a>Выражение поиска

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>Полный URL-адрес

Этот запрос выполняет поиск вакансий, содержащих в начале"prog". В результате будут найдены вакансии со словами "programming" и "programmer". Символ "*" или "?" не может находиться в начале поискового запроса.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  :::image type="content" source="media/search-query-lucene-examples/wildcard.png" alt-text="Запрос с подстановочным знаком" border="false":::

> [!Note]
> Запросы с подстановочными знаками не [анализируются](./search-lucene-query-architecture.md#stage-2-lexical-analysis). Единственное преобразование для неполных терминов запроса — преобразование в нижний регистр.
>

## <a name="next-steps"></a>Следующие шаги
Попробуйте указать средство синтаксического анализа запросов Lucene в коде. Чтобы узнать о том, как настроить запросы поиска для .NET и REST API, воспользуйтесь приведенными ссылками. В этих ссылках используется простой синтаксис по умолчанию, поэтому потребуется применить полученные из этой статьи знания, чтобы задать **queryType**.

* [Запрос индекса с помощью пакета SDK для .NET](./search-get-started-dotnet.md)
* [Запрос индекса с помощью REST API](./search-get-started-powershell.md)

Дополнительные справочные материалы по синтаксису, архитектуре запросов и примеры можно найти по следующим ссылкам:

+ [Примеры запросов с использованием простого синтаксиса](search-query-simple-examples.md)
+ [How full text search works in Azure Cognitive Search](search-lucene-query-architecture.md) (Как выполняется полнотекстовый поиск в Когнитивном поиске Azure)
+ [Простой синтаксис запросов](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Полный синтаксис запроса Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search)