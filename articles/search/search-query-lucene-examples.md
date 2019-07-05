---
title: Примеры запросов Lucene (служба "Поиск Azure")
description: Синтаксис запросов Lucene для поиска нечетких соответствий, поиска с учетом расположения, повышения приоритета терминов, поиска по регулярным выражениям и поиска с использованием подстановочных знаков в службе "Поиск Azure".
author: HeidiSteen
manager: cgronlun
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: affe9084c488984747c4bafca5b8e9536cd6dba8
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485425"
---
# <a name="query-examples-using-full-lucene-search-syntax-advanced-queries-in-azure-search"></a>Примеры запросов, используя синтаксис поиска «full» Lucene (сложные запросы в службе поиска Azure)

При создании запросов для Поиска Azure можно заменить [стандартный синтаксический анализатор](query-simple-syntax.md) по умолчанию на более экспансивное [Средство синтаксического анализа запросов Lucene в Поиск Azure](query-lucene-syntax.md), чтобы сформулировать специализированные и расширенные определения запросов. 

Средство синтаксического анализа Lucene поддерживает сложный запрос конструкций, таких как относящиеся к полям запросов, нечетких соответствий и поиск подстановочный префикс, поиск по сходству, повышение приоритета слов и поиска по регулярным выражениям. Дополнительные возможности поставляются с дополнительными требованиями к обработке, поэтому следует предусмотреть немного больше времени для выполнения. В этой статье можно пошагово выполнить примеры полного синтаксиса операций запросов.

> [!Note]
> Многие специализированные конструкции запросов, обеспечиваемые благодаря полному синтаксису запросов Lucene, не поддерживают [анализ текста](search-lucene-query-architecture.md#stage-2-lexical-analysis), что может оказаться неожиданным, если вы хотите использовать выделение корней слов или лемматизацию. Лексический анализ выполняется только для полными терминами (запрос термина или запрос фразы). Типы запросов с неполными терминами (запрос с префиксом, запрос с подстановочными знаками, запрос с регулярными выражениями, нечеткий запрос) добавляются непосредственно к дереву запроса в обход этапа анализа. Единственное преобразование для неполных терминов запроса — преобразование в нижний регистр. 
>

## <a name="formulate-requests-in-postman"></a>Формирование запросов в Postman

В следующих примерах используется индекс поиска вакансий в Нью-Йорке, состоящий из вакансий, доступных на основе набора данных, полученных в рамках инициативы [City of New York OpenData](https://opendata.cityofnewyork.us/). Эти данные не являются текущими или завершенными. Индекс размещен в службе песочницы корпорации Майкрософт. Это означает, что для использования этих запросов подписка Azure или служба поиска Azure не требуется.

Вам потребуется Postman или аналогичный инструмент для отправки HTTP-запроса GET. Дополнительные сведения см. в статье [Работа с REST API службы "Поиск Azure" с помощью Fiddler или Postman](search-get-started-postman.md).

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

В качестве шага проверки вставьте приведенный ниже запрос в раздел GET и щелкните **Отправить**. Будут возвращены результаты в виде подробных документов JSON. Возвращаются все документы, позволяет просматривать все поля и все значения.

Вставьте этот URL-адрес в клиент REST, проверки, а также для просмотра структуры документа.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=*
  ```

Строка запроса **`search=*`** эквивалентна неточному поиску значения NULL или пустого значения. Это самый простой поиск, можно предпринять.

При необходимости можно добавить **`$count=true`** в URL-адрес, чтобы вернуть количество документов, соответствующих условиям поиска. При пустой строке поиска это будут все документы в индексе (примерно 2800 в случае вакансий в Нью-Йорке).

## <a name="how-to-invoke-full-lucene-parsing"></a>Способы вызова полного синтаксического анализа Lucene

Добавьте **queryType=full**, чтобы вызвать полный синтаксис запроса, переопределив простой синтаксис запроса по умолчанию. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&search=*
```

Во всех примерах в этой статье задается параметр поиска **queryType=full**, указывающий, что полный синтаксис обрабатывается средством синтаксического анализа запросов Lucene. 

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Пример 1 Запрос ограничивается список полей

В первом примере не зависящие от Lucene, но мы привести с ним введения первого запроса фундаментальные понятия: поле области. В этом примере задает область, весь запрос и ответ на несколько определенных полей. Знать, как структурировать читаемый ответ JSON, важно, если используется инструмент Postman или обозреватель поиска. 

Для краткости запрос нацелен только на поле *business_title* и указывает возвращаемые должности. **SearchFields** ограничивает выполнение запроса к полю business_title, и **выберите** указывает, какие поля должны быть включены в ответе.

### <a name="partial-query-string"></a>Строка частичный запрос

```http
&search=*&searchFields=business_title&$select=business_title
```

Ниже приведен тот же запрос с несколькими полями в виде списка с разделителями запятыми.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

Пробелы после запятых являются необязательными.

> [!Tip]
> При использовании REST API из кода приложения, не забудьте параметры кодирования URL-адрес, например `$select` и `searchFields`.

### <a name="full-url"></a>Полный URL-адрес

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

Результат запроса должен выглядеть, как на снимке экрана ниже.

  ![Пример ответа Postman](media/search-query-lucene-examples/postman-sample-results.png)

Вы могли заметить оценку поиска в ответе. Универсальная оценка 1 отображается, если приоритет не указан, потому что выполнен поиск не всего текста или не указано условие. Для нулевого поиска без критериев строки возвращаются в произвольном порядке. При включении критерии поиска, вы увидите поиска оценок развиться значимые значения.

## <a name="example-2-fielded-search"></a>Пример 2 Относящегося к полю поиска

Полный синтаксис Lucene поддерживает области отдельных выражений к определенному полю. В этом примере выполняется поиск должностей, старший термин в них, но не "junior".

### <a name="partial-query-string"></a>Строка частичный запрос

```http
$select=business_title&search=business_title:(senior NOT junior)
```

Ниже приведен тот же запрос с несколькими полями.

```http
$select=business_title, posting_type&search=business_title:(senior NOT junior) AND posting_type:external
```

### <a name="full-url"></a>Полный URL-адрес

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&$select=business_title&search=business_title:(senior NOT junior)
```

  ![Пример ответа Postman](media/search-query-lucene-examples/intrafieldfilter.png)

Вы можете определить операцию относящегося к полю поиска с **fieldName:searchExpression** синтаксис, где выражение поиска может быть одним словом или фразой или более сложное выражение в круглые скобки, иногда с логическими операторами. Некоторые примеры:

- `business_title:(senior NOT junior)`
- `state:("New York" OR "New Jersey")`
- `business_title:(senior NOT junior) AND posting_type:external`

Обязательно добавьте несколько строк в кавычки, если вы хотите, чтобы обе строки считались одной сущности, как в этом случае поиск для двух различных местах в `state` поля. Кроме того, оператор должен быть указан в верхнем регистре, как в случае с NOT и AND.

Поля, указанного в **fieldName:searchExpression** должно быть полем для поиска. Дополнительные сведения об использовании атрибутов индекса в определениях полей см. в статье [Create Index (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) (Создание индексов (REST API службы поиска Azure)).

> [!NOTE]
> В приведенном выше примере, нам удалось не нужно использовать `searchFields` параметра, так как каждая часть запроса имеет имя поля указано явным образом. Тем не менее, можно по-прежнему использовать `searchFields` параметр, если вы хотите выполнить запрос, где некоторые компоненты относятся к определенному полю, а остальные удалось применить к нескольким полям. Например, запрос `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` будет соответствовать `senior NOT junior` только к `business_title` поле, пока оно будет совпадать с «external» `posting_type` поля. Имя поля, указанное в **fieldName:searchExpression** всегда имеет приоритет над `searchFields` параметр, являющийся почему в этом примере, нам не нужно включать `business_title` в `searchFields` параметра.

## <a name="example-3-fuzzy-search"></a>Пример 3 Поиск нечетких соответствий

Полный синтаксис Lucene также поддерживает поиск нечетких соответствий, то есть сопоставление терминов с подобной конструкцией. Чтобы выполнить поиск нечетких соответствий, необходимо добавить символ тильды `~` в конце слова. Дополнительно можно поставить цифру от 0 до 2, указывающую расстояние редактирования. Например, `blue~` или `blue~1` вернет результаты с blue, blues и glue.

### <a name="partial-query-string"></a>Строка частичный запрос

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

Фразы не поддерживаются непосредственно, но нечеткого соответствия можно указать на составные части фразы.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>Полный URL-адрес

Этот запрос осуществляет поиск вакансий со словом "associate" (намеренно написанного с ошибкой).

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![Ответ на поиск нечетких соответствий](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> Запросы с нечетким соответствием не [анализируются](search-lucene-query-architecture.md#stage-2-lexical-analysis). Типы запросов с неполными терминами (запрос с префиксом, запрос с подстановочными знаками, запрос с регулярными выражениями, нечеткий запрос) добавляются непосредственно к дереву запроса в обход этапа анализа. Единственное преобразование для неполных терминов запроса — преобразование в нижний регистр.
>

## <a name="example-4-proximity-search"></a>Пример 4. Поиск с учетом расположения
Операция поиска с учетом расположения позволяет найти слова, расположенные рядом в документе. Вставьте символ тильды "~" в конце фразы, а затем — цифру, обозначающую количество слов, определяющее границу близости. Например, если ввести "hotel airport"~5, будут найдены слова "hotel" и "airport", расположенные в пределах 5 слов друг от друга в документе.

### <a name="partial-query-string"></a>Строка частичный запрос

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>Полный URL-адрес

Этот запрос осуществляет поиск вакансий, содержащих словосочетание "senior analyst", разделенное не более чем одним словом.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  ![Запрос с учетом расположения](media/search-query-lucene-examples/proximity-before.png)

Выполните эту операцию поиска снова, при этом не допуская разделения словосочетания "senior analyst" другими словами. Обратите внимание на то, что для этого запроса возвращены 8 документов, в отличие от 10 документов для предыдущего запроса.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Пример 5. Повышение приоритета терминов
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


## <a name="example-6-regex"></a>Пример 6. Регулярное выражение

Операция поиска по регулярным выражениям позволяет найти совпадение в зависимости от содержимого между косыми чертами "/", как указано в документации [класса RegExp](https://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

### <a name="partial-query-string"></a>Строка частичный запрос

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>Полный URL-адрес

В этом запросе выполняется поиск должностей с термин старший или "junior": `search=business_title:/(Sen|Jun)ior/`.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  ![Запрос регулярных выражений](media/search-query-lucene-examples/regex.png)

> [!Note]
> Запросы с регулярными выражениями не [анализируются](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). Единственное преобразование для неполных терминов запроса — преобразование в нижний регистр.
>

## <a name="example-7-wildcard-search"></a>Пример 7. Поиск с использованием подстановочных знаков
Вы можете использовать распознаваемый синтаксис для поиска с использованием одного (?) или нескольких (\*) подстановочных знаков. Обратите внимание, что средство синтаксического анализа запросов Lucene поддерживает использование этих символов для поиска одного слова, а не фразы.

### <a name="partial-query-string"></a>Строка частичный запрос

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>Полный URL-адрес

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

* [Отправка запросов в индекс службы поиска Azure с помощью пакета SDK для .NET](search-query-dotnet.md)
* [Отправка запросов в индекс службы поиска Azure с помощью REST API](search-create-index-rest-api.md)

Дополнительные справочные материалы по синтаксису, архитектуре запросов и примеры можно найти по следующим ссылкам:

+ [Примеры запросов с использованием простого синтаксиса](search-query-simple-examples.md)
+ [Как работает полнотекстовый поиск в службе поиска Azure](search-lucene-query-architecture.md)
+ [Синтаксис простых запросов](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene query syntax in Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) (Синтаксис запросов Lucene в службе поиска Azure)