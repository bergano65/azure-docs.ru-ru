---
title: Переменные HTTP для обработчика правил Azure CDN | Документация Майкрософт
description: Переменные HTTP позволяют получить метаданные запросов и ответов HTTP.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: 53ad0c516547e17801bd57c2fd6b0d1704383797
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593820"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Переменные HTTP для обработчика правил Azure CDN
Переменные HTTP предоставляют средство, с помощью которого можно получить метаданные запросов и ответов HTTP. Эти метаданные можно использовать, чтобы динамически изменить запрос или ответ. Использование переменных HTTP ограничивается следующими функциями обработчика правил:

- [Перезапись ключа кэша](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Заголовок запроса Modify Client](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Заголовок ответа Modify Client](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [Перенаправление URL-адреса](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [Переопределение URL-адреса](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Определения
В следующей таблице описываются поддерживаемые переменные HTTP. Если метаданные геообъекта (например, почтовый индекс) недоступны для конкретного запроса, возвращается пустое значение.


| ИМЯ | Переменная | Описание | Образец значения |
| ---- | -------- | ----------- | ------------ |
| ASN (инициатор запроса) | %{geo_asnum} | Указывает номер AS инициатора запроса. <br /><br />**Нерекомендуемая:** %{virt_dst_asnum}. <br />Эта переменная является нерекомендуемой. Вместо нее используется %{geo_asnum}. Несмотря на то что правило, которое использует эту нерекомендуемую переменную, будет продолжать работать, необходимо изменить его так, чтобы использовалась новая переменная. | AS15133 |
| Город (инициатор запроса) | %{geo_city} | Указывает город инициатора запроса. | Лос-Анджелес |
| Континент (инициатор запроса) | %{geo_continent} | Указывает континент инициатора запроса в виде сокращения. <br />Допустимые значения: <br />AF: Африка<br />КАК И В СЛУЧАЕ: Азия<br />ЕВРОПА: Европа<br />Н/Д: Северная Америка<br />OC: Океания<br />SA: Северная Америка<br /><br />**Нерекомендуемая:** %{virt_dst_continent}. <br />Эта переменная является устаревшим % {geo_continent}. <br />Несмотря на то что правило, которое использует эту нерекомендуемую переменную, будет продолжать работать, необходимо изменить его так, чтобы использовалась новая переменная.| Н/Д |
| Значение файла cookie | %{cookie_Cookie} | Возвращает значение, соответствующее ключу cookie, определенному в термине Cookie. | Пример использования: <br />%{cookie__utma}<br /><br />Пример значения:<br />111662281.2.10.1222100123 |
| Страна (инициатор запроса) | %{geo_country} | Указывает страну происхождения инициатора запроса с помощью кода страны. <br />**Нерекомендуемая:** %{virt_dst_country}. <br /><br />Эта переменная является нерекомендуемой. Вместо нее используется %{geo_country}. Несмотря на то что правило, которое использует эту нерекомендуемую переменную, будет продолжать работать, необходимо изменить его так, чтобы использовалась новая переменная. | США |
| Назначенная рыночная сфера (инициатор запроса) | %{geo_dma_code} |Указывает медийный рынок инициатора запроса с помощью кода региона. <br /><br />Это поле применимо только для запросов, исходящих из США.| 745 |
| Метод HTTP-запроса | %{request_method} | Указывает метод HTTP-запроса. | GET |
| Код состояния HTTP | %{status} | Указывает код состояния HTTP для ответа. | 200 |
| IP-адрес (инициатор запроса) | %{virt_dst_addr} | Указывает IP-адрес инициатора запроса. | 192.168.1.1 |
| Широта (инициатор запроса) | %{geo_latitude} | Указывает широту инициатора запроса. | 34,0995 |
| Долгота (инициатор запроса) | %{geo_longitude} | Указывает долготу инициатора запроса. | −118,4143 |
| Агломерация, статистическая область (инициатор запроса) | %{geo_metro_code} | Указывает агломерацию инициатора запроса. <br /><br />Это поле применимо только для запросов, исходящих из США.<br />| 745 |
| Порт (инициатор запроса) | %{virt_dst_port} | Указывает временный порт инициатора запроса. | 55885 |
| Почтовый индекс (инициатор запроса) | %{geo_postal_code} | Указывает почтовый индекс инициатора запроса. | 90210 |
| Найдена строка запроса | %{is_args} | Значение этой переменной зависит от того, содержит ли запрос строку запроса.<br /><br />— строка запроса найдена: ?<br />— Без строки запроса: NULL | ? |
| Найден параметр строки запроса | %{is_amp} | Значение этой переменной зависит от того, содержит ли запрос как минимум один параметр строки запроса.<br /><br />— найден параметр: &<br />— Без параметров: NULL | & |
| Значение параметра строки запроса | %{arg_&lt;parameter&gt;} | Возвращает значение, соответствующее параметру строки запроса, определенному в термине &lt;parameter&gt;. | Пример использования: <br />%{arg_language}<br /><br />Пример параметра строки запроса: <br />?language=en<br /><br />Пример значения: en |
| Значение строки запроса | %{query_string} | Указывает целое значение строки запроса, определенное в URL-адресе запроса. |key1=val1&key2=val2&key3=val3 |
| Домен источника ссылки | %{referring_domain} | Указывает домен, определенный в заголовке запроса источника ссылки. | < www.google.com> |
| Регион (инициатор запроса) | %{geo_region} | Указывает регион инициатора запроса (например, область или край) с помощью буквенно-цифрового сокращения. | CA |
| Значение заголовка запроса | %{http_RequestHeader} | Возвращает значение, соответствующее заголовку запроса, определенному в термине RequestHeader. <br /><br />Если имя заголовка запроса содержит дефис (например, User-Agent), замените его символом подчеркивания (например, User_Agent).| Пример использования: %{http_Connection}<br /><br />Пример значения: Keep-Alive | 
| Узел запроса | %{host} | Указывает узел, определенный в URL-адресе запроса. | < www.mydomain.com> |
| Протокол запроса | %{request_protocol} | Указывает протокол запроса. | HTTP/1.1 |
| Схема запроса | %{scheme} | Указывает схему запроса. |http |
| URI запроса (относительное значение) | %{request_uri} | Указывает относительный путь, включая строку запроса, определенную в URI запроса. | /marketing/foo.js?loggedin=true |
| URI запроса (относительное значение без строки запроса) | %{uri} | Указывает относительный путь к запрошенному содержимому. <br /><br/>Основные сведения<br />— Этот относительный путь не содержит строку запроса.<br />— Этот относительный путь отражает перезаписывания URL-адреса. URL-адрес будет перезаписан при следующих условиях:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Функция перезаписи URL-адрес: Эта функция перезаписывает относительный путь, определенный в URI запроса.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-URL-адрес граничной записи CNAME: Этот тип запроса перезаписывается соответствующий URL-адрес CDN. |/800001/corigin/rewrittendir/foo.js |
| URI запроса | %{request} | Описывает запрос. <br />Синтаксис: &lt;Метод HTTP&gt; &lt;относительный путь&gt; &lt;протокола HTTP&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| Значение заголовка ответа | %{resp_&lt;ResponseHeader&gt;} | Возвращает значение, соответствующее заголовку ответа, определенному в термине &lt;ResponseHeader&gt;. <br /><br />Если имя заголовка ответа содержит дефис (например, User-Agent), замените его символом подчеркивания (например, User_Agent). | Пример использования: %{resp_Content_Length}<br /><br />Пример значения: 100 |

## <a name="usage"></a>Использование
В следующей таблице описывается правильный синтаксис для указания переменной HTTP.


| Синтаксис | Пример | Описание |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | Используйте следующий синтаксис для получения всего значения, соответствующего указанной переменной &lt;HTTPVariable&gt;. |
| %{&lt;HTTPVariableDelimiter&gt;} | %{host,} | Используйте следующий синтаксис для установки регистра всего значения, соответствующего указанной переменной &lt;HTTPVariableDelimiter&gt;. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80} | Используйте регулярное выражение для параметра &lt;HTTPVariableDelimiterExpression&gt;, чтобы заменить, удалить или обработать значение переменной HTTP. |

Имена переменных HTTP поддерживают только алфавитные символы и символы подчеркивания. Преобразуйте неподдерживаемые символы в символы подчеркивания.

## <a name="delimiter-reference"></a>Справочные материалы по разделителю
Разделитель можно указать после переменной HTTP, чтобы достичь следующего:

- Преобразовать значение, связанное с переменной.

     Пример: Полностью преобразовать значение в нижний регистр.

- Удалить значение, связанное с переменной.

- Обработать значение, связанное с переменной.

     Пример: Использование регулярных выражений, чтобы изменить значение, связанное с переменной HTTP.

Разделители описаны в следующей таблице.

| Разделитель | Описание |
| --------- | ----------- |
| := | Указывает, что значение по умолчанию будет назначено переменной, если к ней применим один из следующих вариантов: <br />— она отсутствует; <br />— ей задано значение NULL. |
| :+ | Указывает, что значение по умолчанию будет назначено переменной, если для нее уже назначено значение. |
| : | Указывает, что подстрока значения, назначенного переменной, будет развернута. |
| # | Указывает, что шаблон, указанный после этого разделителя, необходимо удалить, если он находится в начале значения, связанного с переменной. |
| % | Указывает, что шаблон, указанный после этого разделителя, необходимо удалить, если он находится в конце значения, связанного с переменной. <br />Это определение применимо, только если в качестве разделителя используется символ %. |
| / | Разделяет переменную HTTP или шаблон. |
| // | Находит и заменяет все экземпляры указанного шаблона. |
| /= | Найти, скопировать и перезаписать все вхождения указанного шаблона. |
| , | Преобразовывает значение, связанное с переменной HTTP, в нижний регистр. |
| ^ | Преобразовывает значение, связанное с переменной HTTP, в верхний регистр. |
| ,, | Преобразовывает все экземпляры указанного символа в значение, связанное с переменной HTTP, в нижний регистр. |
| ^^ | Преобразовывает все экземпляры указанного символа в значение, связанное с переменной HTTP, в верхний регистр. |

## <a name="exceptions"></a>Исключения
В следующей таблице описаны условия, при которых указанный текст не рассматривается как переменная HTTP.

| Условие | Описание | Пример |
| --------- | ----------- | --------|
| Экранирование символа % | Символ процента можно экранировать с использованием обратной косой черты. <br />Пример значения справа будет рассматриваться как литеральное значение, а не как переменная HTTP.| \%{host} |
| Unknown variables | An empty string is always returned for unknown variables. | %{unknown_variable} |
| Invalid characters or syntax | Variables that contain invalid characters or syntax are treated as literal values. <br /><br />Example #1: The specified value contains an invalid character (for example, -). <br /><br />Example #2: The specified value contains a double set of curly braces. <br /><br />Example #3: The specified value is missing a closing curly brace.<br /> | Example #1: %{resp_user-agent} <br /><br />Example #2: %{{host}} <br /><br />Example #3: %{host |
| Missing variable name | A NULL value is always returned when a variable is not specified. | %{} |
| Trailing characters | Characters that trail a variable are treated as literal values. <br />The sample value to the right contains a trailing curly brace that will be treated as a literal value. | %{host}} |

## Setting default header values
A default value can be assigned to a header when it meets any of the following conditions:
- Missing/unset
- Set to NULL.

The following table describes how to define a default value.

| Condition | Syntax | Example | Description |
| --------- | ------ | --------| ----------- |
| Set a header to a default value when it meets any of the following conditions: <br /><br />- Missing Header <br /><br />- Header value is set to NULL.| %{Variable:=Value} | %{http_referrer:=unspecified} | The Referrer header will only be set to *unspecified* when it is either missing or set to NULL. No action will take place if it has been set. |
| Set a header to a default value when it is missing. | %{Variable=Value} | %{http_referrer=unspecified} | The Referrer header will only be set to *unspecified* when it is missing. No action will take place if it has been set. |
| Set the header to a default value when it does not meet any of the following conditions: <br /><br />- Missing<br /><br /> - Set to NULL. | %{Variable:+Value} | %{http_referrer:+unspecified} | The Referrer header will only be set to *unspecified* when a value has been assigned to it. No action will take place if it is either missing or set to NULL. |

## Manipulating variables
Variables can be manipulated in the following ways:

- Expanding substrings
- Removing patterns

### Substring expansion
By default, a variable will expand to its full value. Use the following syntax to only expand a substring of the variable's value.

`%<Variable>:<Offset>:<Length>}`

Key information:

- The value assigned to the Offset term determines the starting character of the substring:

     - Positive: The starting character of the substring is calculated from the first character in the string.
     - Zero: The starting character of the substring is the first character in the string.
     - Negative: The starting character of the substring is calculated from the last character in the string.

- The length of the substring is determined by the *Length* term:

     - Omitted: Omitting the Length term allows the substring to include all characters between the starting character and the end of the string.
     - Positive: Determines the length of the substring from the starting character to the right.
     - Negative: Determines the length of the substring from the starting character to the left.

#### Example:

The following example relies on the following sample request URL:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

The following string demonstrates various methods for manipulating variables:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Based on the sample request URL, the above variable manipulation will produce the following value:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### Pattern removal
Text that matches a specific pattern can be removed from either the beginning or the end of a variable's value.

| Syntax | Action |
| ------ | ------ |
| %{Variable#Pattern} | Remove text when the specified pattern is found at the beginning of a variable's value. |
| %{Variable%Pattern} | Remove text when the specified pattern is found at the end of a variable's value. |

#### Example:

In this sample scenario, the *request_uri* variable is set to:

`/800001/myorigin/marketing/product.html?language=en-US`

The following table demonstrates how this syntax works.

| Sample syntax | Results | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Because the variable starts with the pattern, it was replaced. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Because the variable doesn't end with the pattern, there was no change.|

### Find and replace
The find and replace syntax is described in the following table.

| Syntax | Action |
| ------ | ------ |
| %{Variable/Find/Replace} | Find and replace first occurrence of the specified pattern. |
| %{Variable//Find/Replace} | Find and replace all occurrences of the specified pattern. |
| %{Variable^} |Convert the entire value to uppercase. |
| %{Variable^Find} | Convert the first occurrence of the specified pattern to uppercase. |
| %{Variable,} | Convert the entire value to lowercase. |
| %{Variable,Find} | Convert the first occurrence of the specified pattern to lowercase. |

### Find and rewrite
For a variation on find and replace, use the text that matches the specified pattern when rewriting it. The find and rewrite syntax is described in the following table.

| Syntax | Action |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | Find, copy, and rewrite all occurrences of the specified pattern. |
| %{Variable/^Find/Rewrite} | Find, copy, and rewrite the specified pattern when it occurs at the start of the variable. |
| %{Variable/$Find/Rewrite} | Find, copy, and rewrite the specified pattern when it occurs at the end of the variable. |
| %{Variable/Find} | Find and delete all occurrences of the specified pattern. |

Key information:

- Expand text that matches the specified pattern by specifying a dollar sign followed by a whole integer (for example, $1).

- Multiple patterns can be specified. The order in which the pattern is specified determines the integer that will be assigned to it. In the following example, the first pattern matches "www.," the second pattern matches the second-level domain, and the third pattern matches the top-level domain:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- The rewritten value can consist of any combination of text and these placeholders.

    In the previous example, the hostname is rewritten to `cdn.$2.$3:80` (for example, cdn.mydomain.com:80).

- The case of a pattern placeholder (for example, $1) can be modified through the following flags:
     - U: Uppercase the expanded value.

         Sample syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Lowercase the expanded value.

         Sample syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- An operator must be specified before the pattern. The specified operator determines the pattern-capturing behavior:

     - `=`: Indicates that all occurrences of the specified pattern must be captured and rewritten.
     - `^`: Indicates that only text that starts with the specified pattern will be captured.
     - `$`: Indicates that only text that ends with the specified pattern will be capture.
 
- If you omit the */Rewrite* value, the text that matches the pattern is deleted.

\`{host}le: HTTP variables for Azure CDN rules engine | Microsoft Docs
description: HTTP variables allow you to retrieve HTTP request and response metadata.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''

ms.assetid: 
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus


---
# HTTP variables for Azure CDN rules engine
HTTP variables provide the means through which you can retrieve HTTP request and response metadata. This metadata can then be used to dynamically alter a request or a response. The use of HTTP variables is restricted to the following rules engine features:

- [Cache-Key Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Modify Client Request Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Modify Client Response Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [URL Redirect](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [URL Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## Definitions
The following table describes the supported HTTP variables. A blank value is returned when GEO metadata (for example, postal code) is unavailable for a particular request.


| Name | Variable | Description | Sample value |
| ---- | -------- | ----------- | ------------ |
| ASN (Requester) | %{geo_asnum} | Indicates the requester's AS number. <br /><br />**Deprecated:** %{virt_dst_asnum}. <br />This variable has been deprecated in favor of %{geo_asnum}. Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable. | AS15133 |
| City (Requester) | %{geo_city} | Indicates the requester's city. | Los Angeles |
| Continent (Requester) | %{geo_continent} | Indicates the requester's continent through its abbreviation. <br />Valid values are: <br />AF: Africa<br />AS: Asia<br />EU: Europe<br />NA: North America<br />OC: Oceania<br />SA: South America<br /><br />**Deprecated:** %{virt_dst_continent}. <br />This variable has been deprecated in favor of %{geo_continent}. <br />Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable.| N/A |
| Cookie Value | %{cookie_Cookie} | Returns the value corresponding to the cookie key identified by the Cookie term. | Sample Usage: <br />%{cookie__utma}<br /><br />Sample Value:<br />111662281.2.10.1222100123 |
| Country (Requester) | %{geo_country} | Indicates the requester's country of origin through its country code. <br />**Deprecated:** %{virt_dst_country}. <br /><br />This variable has been deprecated in favor of %{geo_country}. Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable. | US |
| Designated Market Area (Requester) | %{geo_dma_code} |Indicates the requester's media market by its region code. <br /><br />This field is only applicable to requests that originate from the United States.| 745 |
| HTTP Request Method | %{request_method} | Indicates the HTTP request method. | GET |
| HTTP Status Code | %{status} | Indicates the HTTP status code for the response. | 200 |
| IP Address (Requester) | %{virt_dst_addr} | Indicates the requester's IP address. | 192.168.1.1 |
| Latitude (Requester) | %{geo_latitude} | Indicates the requester's latitude. | 34.0995 |
| Longitude (Requester) | %{geo_longitude} | Indicates the requester's longitude. | -118.4143 |
| Metropolitan Statistical Area (Requester) | %{geo_metro_code} | Indicates the requester's metropolitan area. <br /><br />This field is only applicable to requests that originate from the United States.<br />| 745 |
| Port (Requester) | %{virt_dst_port} | Indicates the requester's ephemeral port. | 55885 |
| Postal Code (Requester) | %{geo_postal_code} | Indicates the requester's postal code. | 90210 |
| Query String Found | %{is_args} | The value for this variable varies according to whether the request contains a query string.<br /><br />- Query String Found: ?<br />- No Query String: NULL | ? |
| Query String Parameter Found | %{is_amp} | The value for this variable varies according to whether the request contains at least one query string parameter.<br /><br />- Parameter Found: &<br />- No Parameters: NULL | & |
| Query String Parameter Value | %{arg_&lt;parameter&gt;} | Returns the value corresponding to the query string parameter identified by the &lt;parameter&gt; term. | Sample Usage: <br />%{arg_language}<br /><br />Sample Query String Parameter: <br />?language=en<br /><br />Sample Value: en |
| Query String Value | %{query_string} | Indicates the entire query string value defined in the request URL. |key1=val1&key2=val2&key3=val3 |
| Referrer Domain | %{referring_domain} | Indicates the domain defined in the Referrer request header. | <www.google.com> |
| Region (Requester) | %{geo_region} | Indicates the requester's region (for example, state or province) through its alphanumeric abbreviation. | CA |
| Request Header Value | %{http_RequestHeader} | Returns the value corresponding to the request header identified by the RequestHeader term. <br /><br />If the name of the request header contains a dash (for example, User-Agent), replace it with an underscore (for example, User_Agent).| Sample Usage: %{http_Connection}<br /><br />Sample Value: Keep-Alive | 
| Request Host | %{host} | Indicates the host defined in the request URL. | <www.mydomain.com> |
| Request Protocol | %{request_protocol} | Indicates the request protocol. | HTTP/1.1 |
| Request Scheme | %{scheme} | Indicates the request scheme. |http |
| Request URI (Relative) | %{request_uri} | Indicates the relative path, including the query string, defined in the request URI. | /marketing/foo.js?loggedin=true |
| Request URI (Relative without query string) | %{uri} | Indicates the relative path to the requested content. <br /><br/>Key information:<br />- This relative path excludes the query string.<br />- This relative path reflects URL rewrites. A URL will be rewritten under the following conditions:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- URL Rewrite Feature: This feature rewrites the relative path defined in the request URI.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Edge CNAME URL: This type of request is rewritten to the corresponding CDN URL. |/800001/corigin/rewrittendir/foo.js |
| Request URI | %{request} | Describes the request. <br />Syntax: &lt;HTTP method&gt; &lt;relative path&gt; &lt;HTTP protocol&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| Response Header Value | %{resp_&lt;ResponseHeader&gt;} | Returns the value corresponding to the response header identified by the &lt;ResponseHeader&gt; term. <br /><br />If the name of the response header contains a dash (for example, User-Agent), replace it with an underscore (for example, User_Agent). | Sample Usage: %{resp_Content_Length}<br /><br />Sample Value: 100 |

## Usage
The following table describes the proper syntax for specifying an HTTP variable.


| Syntax | Example | Description |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | Use this syntax to get the entire value corresponding to the specified &lt;HTTPVariable&gt;. |
| %{&lt;HTTPVariableDelimiter&gt;} | %{host,} | Use this syntax to set the case for the entire value corresponding to the specified  &lt;HTTPVariableDelimiter&gt;. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80} | Use a regular expression for &lt;HTTPVariableDelimiterExpression&gt; to replace, delete, or manipulate an HTTP variable's value. |

HTTP variable names only support alphabetic characters and underscores. Convert unsupported characters to underscores.

## Delimiter reference
A delimiter can be specified after an HTTP variable to achieve any of the following effects:

- Transform the value associated with the variable.

     Example: Convert the entire value to lowercase.

- Delete the value associated with the variable.

- Manipulate the value associated with the variable.

     Example: Use regular expressions to change the value associated with the HTTP variable.

The delimiters are described in the following table.

| Delimiter | Description |
| --------- | ----------- |
| := | Indicates that a default value will be assigned to the variable when it is either: <br />- Missing <br />- Set to NULL. |
| :+ | Indicates that a default value will be assigned to the variable when a value has been assigned to it. |
| : | Indicates that a substring of the value assigned to the variable will be expanded. |
| # | Indicates that the pattern specified after this delimiter should be deleted when it is found at the beginning of the value associated with the variable. |
| % | Indicates that the pattern specified after this delimiter should be deleted when it is found at the end of the value associated with the variable. <br />This definition is only applicable when the % symbol is used as a delimiter. |
| / | Delimits an HTTP variable or a pattern. |
| // | Find and replace all instances of the specified pattern. |
| /= | Find, copy, and rewrite all occurrences of the specified pattern. |
| , | Convert the value associated with the HTTP variable to lowercase. |
| ^ | Convert the value associated with the HTTP variable to uppercase. |
| ,, | Convert all instances of the specified character in the value associated with the HTTP variable to lowercase. |
| ^^ | Convert all instances of the specified character in the value associated with the HTTP variable to uppercase. |

## Exceptions
The following table describes circumstances under which the specified text isn't treated as an HTTP variable.

| Condition | Description | Example |
| --------- | ----------- | --------|
| Escaping % symbol | The percentage symbol can be escaped through the use of a backslash. <br />The sample value to the right will be treated as a literal value and not as an HTTP variable.| \%{host} |
| Неизвестные переменные | Для неизвестных переменных всегда возвращается пустая строка. | % {unknown_variable} |
| Недопустимые символы или синтаксис | Переменные, содержащие недопустимые символы или синтаксис, обрабатываются как литеральные значения. <br /><br />Пример #1. Указанное значение содержит недопустимый символ (например,-). <br /><br />Пример #2. Указанное значение с набором двойных фигурных скобок. <br /><br />Пример #3. Указанное значение не содержит закрывающую фигурную скобку.<br /> | Пример №1. %{resp_user-agent} <br /><br />Пример №2. %{{host}} <br /><br />Пример №3. %{host |
| Отсутствует имя переменной | Если переменная указана, всегда возвращается значение NULL. | %{} |
| Символы в конце | Символы, которые находятся за переменной, обрабатываются как литеральные значения. <br />Пример значения справа содержит в конце фигурную скобку, которая будет рассматриваться как литеральное значение. | %{host}} |

## <a name="setting-default-header-values"></a>Установка значений заголовков по умолчанию
Заголовку может быть назначено значение по умолчанию при соблюдении любого из следующих условий:
- отсутствует или не задан;
- задано значение NULL.

В следующей таблице описывается, как определить значение по умолчанию.

| Условие | Синтаксис | Пример | Описание |
| --------- | ------ | --------| ----------- |
| Задайте для заголовка значение по умолчанию при соблюдении любого из следующих условий: <br /><br />— заголовок отсутствует; <br /><br />— для заголовка задано значение NULL.| %{Variable:=Value} | %{http_referrer:=unspecified} | Заголовок источника ссылки устанавливается только в *неуказанных* где отсутствует или задано значение NULL. Если он задан, никаких действий не требуется. |
| Задайте для заголовка значение по умолчанию, если он отсутствует. | %{Variable=Value} | %{http_referrer=unspecified} | Заголовок источника ссылки устанавливается только в *неуказанных* при его отсутствии. Если он задан, никаких действий не требуется. |
| Задайте для заголовка значение по умолчанию, если следующие условия не соблюдены: <br /><br />— он отсутствует;<br /><br /> — ему задано значение NULL. | %{Variable:+Value} | % {http_referrer: + неуказанных} | Заголовок источника ссылки устанавливается только в *неуказанных* когда значение было назначено ему. Если он отсутствует или ему задано значение NULL, никаких действий выполнять не требуется. |

## <a name="manipulating-variables"></a>Обработка переменных
Переменные можно обрабатывать следующими способами:

- расширение подстрок;
- удаление шаблонов.

### <a name="substring-expansion"></a>Расширение подстрок
По умолчанию переменная будет расширяться до своего полного значения. Используйте следующий синтаксис, чтобы только расширить подстроку значения переменной.

`%<Variable>:<Offset>:<Length>}`

Основные сведения

- Значение, назначенное термину Offset, определяет начальный символ подстроки.

     - Положительное: Начального символа подстроки вычисляется на основе первого символа в строке.
     - Нуль: Начального символа подстроки является первым символом в строке.
     - Если задано отрицательное значение, Начального символа подстроки вычисляется на основе последнего символа в строке.

- Длина подстроки определяется термином *Length*:

     - Опустить. Пропуск термин длина позволяет подстроке для включения всех символов между символом начала и конца строки.
     - Положительное: Определяет длину подстроки из начального символа справа.
     - Если задано отрицательное значение, Определяет длину подстроки из начального знака слева.

#### <a name="example"></a>Пример:

В следующем примере используется следующий пример URL-адреса запроса:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

Следующая строка демонстрирует различные способы обработки переменных:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

На основании примера URL-адреса запроса обработка приведенной выше переменной выдаст следующее значение:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Удаление шаблона
Текст, соответствующий определенному шаблону, можно удалить из начала или конца значения переменной.

| Синтаксис | Action |
| ------ | ------ |
| %{Variable#Pattern} | Удалите текст, если указанный шаблон находится в начале значения переменной. |
| %{Variable%Pattern} | Удалите текст, если указанный шаблон находится в конце значения переменной. |

#### <a name="example"></a>Пример:

В этом примере сценария переменной *request_uri* присваивается значение:

`/800001/myorigin/marketing/product.html?language=en-US`

В следующей таблице показано, как работает этот синтаксис.

| Пример синтаксиса | Результаты | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Так как переменная начинается с шаблона, он был заменен. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Так как переменная не заканчивается шаблоном, изменения не вносились.|

### <a name="find-and-replace"></a>Поиск и замена
В следующей таблице описан синтаксис поиска и замены.

| Синтаксис | Action |
| ------ | ------ |
| %{Variable/Find/Replace} | Найти и заменить первое вхождение указанного шаблона. |
| %{Variable//Find/Replace} | Найти и заменить все вхождения указанного шаблона. |
| %{Variable^} |Полностью преобразовать значение в верхний регистр. |
| %{Variable^Find} | Преобразовать первое вхождение указанного шаблона в верхний регистр. |
| %{Variable,} | Полностью преобразовать значение в нижний регистр. |
| %{Variable,Find} | Преобразовать первое вхождение указанного шаблона в нижний регистр. |

### <a name="find-and-rewrite"></a>Поиск и перезапись
В качестве разновидности поиска и замены при перезаписи используйте текст, который соответствует указанному шаблону. В следующей таблице описан синтаксис поиска и перезаписи.

| Синтаксис | Action |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | Найти, скопировать и перезаписать все вхождения указанного шаблона. |
| %{Variable/^Find/Rewrite} | Найти, скопировать и перезаписать указанный шаблон, если он находится в начале переменной. |
| %{Variable/$Find/Rewrite} | Найти, скопировать и перезаписать указанный шаблон, если он находится в конце переменной. |
| %{Variable/Find} | Найти и удалить все вхождения указанного шаблона. |

Основные сведения

- Разверните текст, соответствующий указанному шаблону, указав знак доллара и целое число (например, $1).

- Можно указать несколько шаблонов. Порядок, в котором указан шаблон, определяет целое число, которое будет ему назначено. В следующем примере первый шаблон соответствует "www.", второй шаблон — домену второго уровня, а третий шаблон — домену верхнего уровня:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- Перезаписанное значение может включать любое сочетание текста и этих заполнителей.

    В предыдущем примере имя узла переписано как `cdn.$2.$3:80` (например, cdn.mydomain.com:80).

- Регистр заполнителя шаблона (например, $1) можно изменить с помощью следующих флагов:
     - U: В верхнем регистре расширенное значение.

         Пример синтаксиса:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Нижний регистр расширенное значение.

         Пример синтаксиса:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- Перед шаблоном должен быть указан оператор. Указанный оператор определяет поведение захвата шаблона:

     - `=`. Указывает, что все вхождения заданного шаблона должен быть фиксируются и переписать.
     - `^`. Указывает, будет содержаться только текст, который начинается с указанным шаблоном.
     - `$`. Показывает, что только текст, который заканчивается указанному шаблону записи.
 
- Если не указать значение */Rewrite*, текст, который соответствует шаблону, будет удален.


