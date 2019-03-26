---
title: Примеры запросов Lucene (служба "Поиск Azure")
description: Синтаксис запросов Lucene для поиска нечетких соответствий, поиска с учетом расположения, повышения приоритета терминов, поиска по регулярным выражениям и поиска с использованием подстановочных знаков в службе "Поиск Azure".
author: HeidiSteen
manager: cgronlun
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: c7c120b9bac33f71df72650d8a9d9a72e819d227
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439207"
---
# <a name="query-examples-using-full-lucene-search-syntax-advanced-queries-in-azure-search"></a>Примеры запросов, используя синтаксис поиска «full» Lucene (сложные запросы в службе поиска Azure)

При создании запросов для Поиска Azure можно заменить [стандартный синтаксический анализатор](query-simple-syntax.md) по умолчанию на более экспансивное [Средство синтаксического анализа запросов Lucene в Поиск Azure](query-lucene-syntax.md), чтобы сформулировать специализированные и расширенные определения запросов. 

Средство синтаксического анализа Lucene поддерживает сложный запрос конструкций, таких как относящиеся к полям запросов, нечетких соответствий и поиск подстановочный префикс, поиск по сходству, повышение приоритета слов и поиска по регулярным выражениям. Дополнительные возможности поставляются с дополнительными требованиями к обработке, поэтому следует предусмотреть немного больше времени для выполнения. В этой статье можно пошагово выполнить примеры полного синтаксиса операций запросов.

> [!Note]
> Многие специализированные конструкции запросов, обеспечиваемые благодаря полному синтаксису запросов Lucene, не поддерживают [анализ текста](search-lucene-query-architecture.md#stage-2-lexical-analysis), что может оказаться неожиданным, если вы хотите использовать выделение корней слов или лемматизацию. Лексический анализ выполняется только для полными терминами (запрос термина или запрос фразы). Типы запросов с неполными терминами (запрос с префиксом, запрос с подстановочными знаками, запрос с регулярными выражениями, нечеткий запрос) добавляются непосредственно к дереву запроса в обход этапа анализа. Единственное преобразование для неполных терминов запроса — преобразование в нижний регистр. 
>

## <a name="formulate-requests-in-postman"></a>Формирование запросов в Postman

В следующих примерах используется индекс поиска вакансий в Нью-Йорке, состоящий из вакансий, доступных на основе набора данных, полученных в рамках инициативы [City of New York OpenData](https://opendata.cityofnewyork.us/). Эти данные не являются текущими или завершенными. Индекс размещен в службе песочницы корпорации Майкрософт. Это означает, что для использования этих запросов подписка Azure или служба поиска Azure не требуется.

Вам потребуется Postman или аналогичный инструмент для отправки HTTP-запроса GET. Дополнительные сведения см. в статье [Работа с REST API службы "Поиск Azure" с помощью Fiddler или Postman](search-fiddler.md).

### <a name="set-the-request-header"></a>Настройка заголовка запроса

1. В заголовке запроса задайте значение **Content-Type** для `application/json`.

2. Добавьте элемент **api-key** и задайте для него следующую строку: `252044BE3886FE4A8E3BAA4F595114BB`. Это ключ запроса для службы поиска в песочнице, в которой размещен индекс поиска вакансий Нью-Йорка.

Настроенный заголовок запроса можно многократно использовать для всех запросов в этой статье, только заменяя строку **search=**. 

  ![Заголовок запроса Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Настройка URL-адреса запроса

Запрос представляет собой команду GET и URL-адрес, содержащий строку конечную точку Поиска Azure и строку поиска.

  ![Заголовок запроса Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

URL-адрес содержит следующие элементы.

+ **`https://azs-playground.search.windows.net/`** — служба поиска в песочнице, обслуживаемая командой разработчиков Поиска Azure. 
+ **`indexes/nycjobs/`** — это индекс вакансий Нью-Йорка в коллекции индексов этой службы. Имя службы и индекс должны быть указаны в запросе.
+ **`docs`** — коллекция документов, содержащая все содержимое для поиска. Значение api-key в заголовке запроса подходит только для операций чтения, нацеленных на эту коллекцию документов.
+ **`api-version=2017-11-11`** задает значение api-version. Это обязательный параметр в каждом запросе.
+ **`search=*`** —это строка запроса, которая имеет значение NULL в исходном запросе и возвращает первые 50 результатов (по умолчанию).

## <a name="send-your-first-query"></a>Отправка первого запроса

В качестве шага проверки вставьте приведенный ниже запрос в раздел GET и щелкните **Отправить**. Будут возвращены результаты в виде подробных документов JSON. Возвращаются все документы, позволяет просматривать все поля и все значения.

Вставьте этот URL-адрес в клиент REST, проверки, а также для просмотра структуры документа.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=*
  ```

Строка запроса **`search=*`** эквивалентна неточному поиску значения NULL или пустого значения. Это самый простой поиск, можно предпринять.

При необходимости можно добавить **`$count=true`** в URL-адрес, чтобы вернуть количество документов, соответствующих условиям поиска. При пустой строке поиска это будут все документы в индексе (примерно 2800 в случае вакансий в Нью-Йорке).

## <a name="how-to-invoke-full-lucene-parsing"></a>Способы вызова полного синтаксического анализа Lucene

Добавьте **queryType=full**, чтобы вызвать полный синтаксис запроса, переопределив простой синтаксис запроса по умолчанию. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&search=*
```

Во всех примерах в этой статье задается параметр поиска **queryType=full**, указывающий, что полный синтаксис обрабатывается средством синтаксического анализа запросов Lucene. 

## <a name="example-1-field-scoped-query"></a>Пример 1 Запрос по полям

В первом примере не зависящие от Lucene, но мы привести с ним введения первого запроса фундаментальные понятия: вложения. В этом примере задается область выполнения запроса и для ответа определяется всего несколько конкретных полей. Знать, как структурировать читаемый ответ JSON, важно, если используется инструмент Postman или обозреватель поиска. 

Для краткости запрос нацелен только на поле *business_title* и указывает возвращаемые должности. Синтаксис **searchFields** ограничивает выполнение запроса только полем business_title и **выбирает**, какие поля должны быть включены в ответ.

### <a name="partial-query-string"></a>Строка частичный запрос

```http
&search=*&searchFields=business_title&$select=business_title
```

Ниже приведен тот же запрос с несколькими полями в виде списка с разделителями запятыми.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>Полный URL-адрес

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

Результат запроса должен выглядеть, как на снимке экрана ниже.

  ![Пример ответа Postman](media/search-query-lucene-examples/postman-sample-results.png)

Вы могли заметить оценку поиска в ответе. Универсальная оценка 1 отображается, если приоритет не указан, потому что выполнен поиск не всего текста или не указано условие. Для нулевого поиска без критериев строки возвращаются в произвольном порядке. Когда вы добавите условие поиска, вы увидите, как оценки поиска превратятся в понятные значения.

## <a name="example-2-intra-field-filtering"></a>Пример 2 Фильтрация внутри поля

Полный синтаксис Lucene поддерживает выражения в поле. В этом примере выполняется поиск должностей, старший термин в них, но не "junior".

### <a name="partial-query-string"></a>Строка частичный запрос

```http
searchFields=business_title&$select=business_title&search=business_title:senior+NOT+junior
```

Ниже приведен тот же запрос с несколькими полями.

```http
searchFields=business_title, posting_type&$select=business_title, posting_type&search=business_title:senior+NOT+junior AND posting_type:external
```

### <a name="full-url"></a>Полный URL-адрес

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:senior+NOT+junior
```

  ![Пример ответа Postman](media/search-query-lucene-examples/intrafieldfilter.png)

Задав конструкцию **fieldname:searchterm**, можно определить операции запроса, относящегося к полю, где поле представляет собой одно слово, а условие поиска — одно слово или фразу, иногда с логическими операторами. Некоторые примеры:

* business_title:(senior NOT junior)
* state:("New York" AND "New Jersey")

Добавьте несколько строк в кавычках, если необходимо, чтобы обе строки считались одной сущностью, как в приведенном случае поиска двух разных городов в поле расположения. Кроме того, оператор должен быть указан в верхнем регистре, как в случае с NOT и AND.

Поле, указанное в **fieldname:searchterm**, должно поддерживать возможность поиска. Дополнительные сведения об использовании атрибутов индекса в определениях полей см. в статье [Create Index (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) (Создание индексов (REST API службы поиска Azure)).

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
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
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
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  ![Запрос с учетом расположения](media/search-query-lucene-examples/proximity-before.png)

Выполните эту операцию поиска снова, при этом не допуская разделения словосочетания "senior analyst" другими словами. Обратите внимание на то, что для этого запроса возвращены 8 документов, в отличие от 10 документов для предыдущего запроса.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Пример 5. Повышение приоритета терминов
При повышении приоритета слов документы сортируются по приоритету, т. е. документы, в которых содержится условие поиска, имеют высший приоритет по отношению к документам, в которых его нет. Чтобы повысить приоритет условия поиска, используйте символ крышки "^" и коэффициент повышения приоритета (число) в конце этого условия. 

### <a name="full-urls"></a>Полные URL-адреса

Этот запрос before выполняет поиск вакансий, содержащих термин *computer analyst*. Обратите внимание на то, что вакансии, содержащие оба слова *computer* и *analyst*, не найдены, а вакансии со словом *computer* выведены в верхней части результатов.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  ![Повышение приоритета термина before](media/search-query-lucene-examples/termboostingbefore.png)

В запросе after выполните этот поиск снова, при этом повышая приоритет результатов, содержащих термин *analyst*, относительно результатов, содержащих слово *computer*, если оба слова не встречаются одновременно. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
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
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
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
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
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
+ [Простой синтаксис запросов](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene query syntax in Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) (Синтаксис запросов Lucene в службе поиска Azure)