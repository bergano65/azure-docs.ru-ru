---
title: Сопоставление полей в индексаторах
titleSuffix: Azure Cognitive Search
description: Настройка карт полевых данных в индексаторе с учетом различий в названиях полей и представлениях данных.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3e09741e841897032b8146dee67b79e0c26ea5cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275158"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Полевые картографии и преобразования с помощью индексов когнитивного поиска Azure

При использовании индексов когнитивного поиска Azure иногда обнаруживаем, что данные ввода не совсем совпадают со схемой целевого индекса. В этих случаях можно использовать **полевые отображения** для изменения формы данных в процессе индексации.

Примеры ситуаций, в которых могут пригодиться сопоставления полей:

* У источника данных `_id`есть поле с именем, но Azure Cognitive Search не позволяет именаполей, которые начинаются с подчеркивая. Полевая картографирование позволяет эффективно переименовывать поле.
* Необходимо заполнить несколько полей в индексе из одних и тех же исходных данных данных. Например, в этих областях может потребоваться применить различные анализаторы.
* Необходимо заполнить поле индекса данными из нескольких источников данных, а в источниках данных используются разные названия полей.
* Данные необходимо шифровать или расшифровывать в кодировке Base64. Сопоставления полей поддерживают несколько **функций сопоставления**, включая шифрование и расшифровку в кодировке Base64.

> [!NOTE]
> Функция картирования полей индексеров когнитивного поиска Azure обеспечивает простой способ картирования полей данных для индексирования полей с несколькими вариантами преобразования данных. Для изменения формы, которую легко индексировать, может потребоваться предварительная обработка данных.
>
> Microsoft Azure Data Factory — это мощное облачное решение для импорта и преобразования данных. Вы также можете написать код для преобразования исходных данных перед индексацией. Для примеров кода см. [Model multilevel facets](search-example-adventureworks-multilevel-faceting.md) [Model relational data](search-example-adventureworks-modeling.md)
>

## <a name="set-up-field-mappings"></a>Настройка карт полевых конпой

Сопоставление полей состоит из трех частей:

1. Параметр `sourceFieldName`— представляет поле в источнике данных. Это — обязательное свойство.
2. Необязательный параметр `targetFieldName`— представляет поле в индексе поиска. Если этот параметр опущен, используется такое же имя, как в источнике данных.
3. Необязательный параметр `mappingFunction`— позволяет преобразовывать данные, используя одну из нескольких предопределенных функций. Полный список функций представлен [ниже](#mappingFunctions).

Полевые отображения добавляются в `fieldMappings` массив определения индекса.

## <a name="map-fields-using-the-rest-api"></a>Карты полей с помощью API REST

Можно добавить картографирование полей при создании нового индекса с помощью запроса [API Create Indexer.](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) Управлять картографированием поля существующего индекса с помощью запроса [API индекса обновления.](https://docs.microsoft.com/rest/api/searchservice/update-indexer)

Например, вот как сопоставить поле исходного кода с целевым полем с другим именем:

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

Исходное поле может быть отображено в нескольких картографах. В следующем примере показано, как «развил» поле, копируя одно и то же поле исходного кода на два разных поля индекса:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure Cognitive Search использует нечувствительное сравнение для решения имен полей и функций в картографах полей. Это удобно (допускаются ошибки в выборе регистра), но означает, что источник данных или индекс не может содержать поля, которые различаются только регистром.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Карты полей с помощью .NET SDK

Вы определяете полевые отображения в .NET SDK `SourceFieldName` с `TargetFieldName`помощью `MappingFunction` класса [FieldMapping,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) который имеет свойства и дополнительную ссылку.

Можно указать полевые отображения при построении `Indexer.FieldMappings` индекса или позже, непосредственно установив свойство.

В следующем примере СЗ приведены картографирование полей при построении индекса.

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Функции сопоставления полей

Функция картирования поля преобразует содержимое поля перед его хранением в индексе. В настоящее время поддерживаются следующие функции отображения:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>функция base64Encode

Выполняет *безопасное* кодирование строки входных данных в Base64. Входные данные должны быть в кодировке UTF-8.

#### <a name="example---document-key-lookup"></a>Пример - поиск ключей документа

В ключе документа Azure Cognitive Search могут отображаться только символы, безопасные для URL(ru) только символы, безопасные для URL-адресов, могут отображаться в ключе к использованию когнитивного поиска Azure (потому что клиенты должны иметь возможность обращаться к документу с помощью [API Lookup).](https://docs.microsoft.com/rest/api/searchservice/lookup-document) Если исходное поле для ключа содержит небезопасные символы URL-небезопасных, `base64Encode` функцию можно преобразовать во время индексации. Однако ключ документа (как до, так и после преобразования) не может быть длиннее 1024 символов.

При извлечении закодированного ключа во время поиска `base64Decode` можно использовать функцию для получения исходного значения ключа и использовать его для извлечения исходного документа.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

Если вы не включаете свойство параметров для функции отображения, оно по умолчанию по умолчанию указывает на значение. `{"useHttpServerUtilityUrlTokenEncode" : true}`

Azure Cognitive Search поддерживает два различных кодирования Base64. При кодировании и расшифровке одного поля следует использовать одни и те же параметры. Для получения дополнительной [base64 encoding options](#base64details) информации см.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>функция base64Decode

Выполняет декодирование входной строки в Base64. Предполагается, что входные ввода — это *строка* base64, безопасная для URL.64.

#### <a name="example---decode-blob-metadata-or-urls"></a>Пример - декодирование метаданных капли или URL-адресов

Исходные данные могут содержать закодированные строки Base64, такие как строки метаданных blob или веб-URL-адреса, которые требуется сделать поискпростым как простой текст. Функцию `base64Decode` можно использовать для превращения закодированных данных обратно в обычные строки при заполнении индекса поиска.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

Если вы не включаете свойство параметров, оно `{"useHttpServerUtilityUrlTokenEncode" : true}`по умолчанию по умолчанию указывает на значение.

Azure Cognitive Search поддерживает два различных кодирования Base64. При кодировании и расшифровке одного поля следует использовать одни и те же параметры. Для получения более [base64 encoding options](#base64details) подробной информации см.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>варианты кодирования base64

Azure Cognitive Search поддерживает кодирование кодирования URL-безопасной базы64 и нормальное кодирование base64. Строка, которая является base64, закодированная во время индексации, должна быть расшифрована позже с теми же вариантами кодирования, иначе результат не будет соответствовать оригиналу.

Если `useHttpServerUtilityUrlTokenEncode` или `useHttpServerUtilityUrlTokenDecode` параметры для кодирования и декодирования соответственно `true`настроены, то `base64Encode` ведет себя как [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) и `base64Decode` ведет себя как [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

> [!WARNING]
> Если `base64Encode` используется для получения ключевых значений, `useHttpServerUtilityUrlTokenEncode` должны быть установлены на истину. Для ключевых значений можно использовать только кодирование защищенной от URL 64 базы. См [правила именования &#40;Azure Cognitive Search&#41;](https://docs.microsoft.com/rest/api/searchservice/naming-rules) для полного набора ограничений на символы в ключевых значениях.

Библиотеки .NET в Azure Cognitive Search предполагают полную рамку .NET, которая обеспечивает встроенное кодирование. Параметры `useHttpServerUtilityUrlTokenEncode` `useHttpServerUtilityUrlTokenDecode` и опции используют эту встроенную функциональность. Если вы используете .NET Core или другую `false` платформу, мы рекомендуем настроить эти параметры и напрямую назовем функции кодирования и декодирования вашей инфраструктуры.

В следующей таблице сравниваются различные кодировки Base64 строки `00>00?00`. Чтобы определить требуемую дополнительную обработку (при наличии) для функций Base64, примените функцию кодировки библиотеки в строке `00>00?00` и сравните выходные данные с ожидаемыми выходными данными `MDA-MDA_MDA`.

| Кодирование | Выводные данные кодировки Base64 | Дополнительная обработка после кодирования библиотеки | Дополнительная обработка перед кодированием библиотеки |
| --- | --- | --- | --- |
| Base64 с заполнением | `MDA+MDA/MDA=` | Использовать безопасные знаки URL-адреса и удалить заполнение | Использовать стандартные знаки Base64 и добавить заполнение |
| Base64 без заполнения | `MDA+MDA/MDA` | Использовать безопасные знаки URL-адреса | Использовать стандартные знаки Base64 |
| Безопасное кодирование строки вводных данных в Base64 с заполнением | `MDA-MDA_MDA=` | Удаление заполнения | Добавление заполнения |
| Безопасное кодирование строки вводных данных в Base64 без заполнения | `MDA-MDA_MDA` | None | None |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>функция extractTokenAtPosition

Разбивает поле строки, используя заданный разделитель, и выбирает маркер из указанной позиции в результате разбиения.

Эта функция использует следующие параметры:

* `delimiter`: строка, которая используемая как разделитель при разбиении входной строки.
* `position`: целочисленная нулевая позиция маркера, которую нужно выбрать после разбиения входной строки.

Например, если при входных данных `Jane Doe` параметр `delimiter` имеет значение `" "` (пробел), а параметр `position` — значение 0, то результатом будет `Jane`; если параметр `position` имеет значение 1, то результатом будет `Doe`. Если позиция ссылается на несуществующий токен, выдается ошибка.

#### <a name="example---extract-a-name"></a>Пример - извлечь имя

Источник данных содержит поле `PersonName`, и вам нужно индексировать его как два отдельных поля — `FirstName` и `LastName`. Эта функция позволяет разбить входные данные, используя пробел как разделитель.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>функция jsonArrayToStringCollection

Преобразует строку, отформатированную как массив строк JSON, в массив строк, который можно использовать для заполнения поля `Collection(Edm.String)` в индексе.

Например, если исходной строкой является `["red", "white", "blue"]`, то целевое поле типа `Collection(Edm.String)` будет заполнено следующими тремя значениями: `red`, `white` и `blue`. Если входные значения нельзя проанализировать как массивы строк JSON, выдается ошибка.

#### <a name="example---populate-collection-from-relational-data"></a>Пример - сбор заселений из реляционных данных

База данных Azure S'L не имеет встроенного `Collection(Edm.String)` типа данных, который естественным образом отображает поля в Azure Cognitive Search. Чтобы заполнить поля сбора строк, можно предварительно обработать исходные данные в `jsonArrayToStringCollection` виде массива строки JSON, а затем использовать функцию отображения.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

Для подробного примера, который преобразует реляционные данные в поля сбора индексов, [см.](search-example-adventureworks-modeling.md)

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>функция urlEncode

Эта функция может быть использована для кодирования строки, чтобы она была "URL безопасной". При использовании строки, содержащей символы, которые не допускаются в URL, эта функция преобразует эти "небезопасные" символы в эквиваленты сущности символов. Эта функция использует формат кодирования UTF-8.

#### <a name="example---document-key-lookup"></a>Пример - поиск ключей документа

`urlEncode`функция может быть использована `base64Encode` в качестве альтернативы функции, если только URL небезопасные символы должны быть преобразованы, сохраняя при этом другие символы как есть.

Скажем, строка ввода - `<hello>` тогда целевое поле типа `(Edm.String)` будет заселено значением`%3chello%3e`

При извлечении закодированного ключа во время поиска `urlDecode` можно использовать функцию для получения исходного значения ключа и использовать его для извлечения исходного документа.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "urlEncode"
    }
  }]
 ```

 <a name="urlDecodeFunction"></a>

 ### <a name="urldecode-function"></a>функция urlDecode

 Эта функция преобразует строку, закодированную URL, в декодированную строку с помощью формата кодирования UTF-8.

 ### <a name="example---decode-blob-metadata"></a>Пример - декодирование метаданных капли

 Некоторые клиенты хранения данных Azure автоматически кодируют метаданные blob, если они содержат символы, не относясь к ASCII. Однако, если вы хотите сделать такие метаданные поиска (как простой текст), вы можете использовать `urlDecode` функцию, чтобы превратить закодированные данные обратно в обычные строки при заполнении индекса поиска.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "UrlEncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : {
      "name" : "urlDecode"
    }
  }]
 ```
 
 <a name="fixedLengthEncodeFunction"></a>
 
 ### <a name="fixedlengthencode-function"></a>фиксированная функция LengthEncode
 
 Эта функция преобразует строку любой длины в строку фиксированной длины.
 
 ### <a name="example---map-document-keys-that-are-too-long"></a>Пример - слишком длинные ключи документа карты
 
При возникновении ошибок, жалующихся на то, что ключ документа длиннее 1024 символов, эта функция может быть применена для уменьшения длины ключа документа.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "metadata_storage_path",
    "targetFieldName" : "your key field",
    "mappingFunction" : {
      "name" : "fixedLengthEncode"
    }
  }]
 ```
