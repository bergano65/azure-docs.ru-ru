---
title: Сопоставления полей для автоматического индексирования с помощью индексаторов в службе "Поиск Azure"
description: Сведения о настройке сопоставлений полей индексатора в службе "Поиск Azure" для выявления различий в названиях полей и представлениях данных
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 771a6e413cd08a338da41c09cd6a0da35e28e5e4
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840659"
---
# <a name="field-mappings-and-transformations-using-azure-search-indexers"></a>Сопоставления полей и преобразования с помощью индексаторов поиска Azure

При использовании индексаторов поиска Azure иногда оказывается, что входные данные не соответствуют схеме целевого индекса. В таких случаях можно использовать **сопоставления полей** для перерисовки данных во время процесса индексирования.

Примеры ситуаций, в которых могут пригодиться сопоставления полей:

* Источник данных имеет поле с именем `_id`, но поиск Azure не допускает имена полей, начинающихся с символа подчеркивания. Сопоставление полей позволяет эффективно переименовывать поля.
* Необходимо заполнить несколько полей в индексе из одних и тех же данных источника данных. Например, может потребоваться применить к этим полям разные анализаторы.
* Необходимо заполнить поле индекса данными из нескольких источников данных, а в каждом из источников данных будут использоваться разные имена полей.
* Данные необходимо шифровать или расшифровывать в кодировке Base64. Сопоставления полей поддерживают несколько **функций сопоставления**, включая шифрование и расшифровку в кодировке Base64.

> [!NOTE]
> Функция сопоставления полей индексаторов службы поиска Azure предоставляет простой способ сопоставления полей данных с полями индекса, а также несколько параметров для преобразования данных. Более сложные данные могут потребовать предварительной обработки для перерисовки в форме, которую можно легко индексировать.
>
> Фабрика данных Microsoft Azure — это мощное облачное решение для импорта и преобразования данных. Можно также написать код для преобразования исходных данных перед индексированием. Примеры кода см. в разделе [моделирование реляционных данных](search-example-adventureworks-modeling.md) и [многоуровневых аспектов модели](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Настройка сопоставлений полей

Сопоставление полей состоит из трех частей:

1. Параметр `sourceFieldName`— представляет поле в источнике данных. Это — обязательное свойство.
2. Необязательный параметр `targetFieldName`— представляет поле в индексе поиска. Если этот параметр опущен, используется такое же имя, как в источнике данных.
3. Необязательный параметр `mappingFunction`— позволяет преобразовывать данные, используя одну из нескольких предопределенных функций. Полный список функций представлен [ниже](#mappingFunctions).

Сопоставления полей добавляются в `fieldMappings` массив определения индексатора.

## <a name="map-fields-using-the-rest-api"></a>Сопоставьте поля с помощью REST API

Можно добавить сопоставления полей при создании нового индексатора с помощью запроса API [создания индексатора](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) . Вы можете управлять сопоставлениями полей существующего индексатора с помощью запроса API [обновления индексатора](https://docs.microsoft.com/rest/api/searchservice/update-indexer) .

Например, вот как можно соотнести исходное поле с целевым полем с другим именем:

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

На исходное поле можно ссылаться в нескольких сопоставлениях полей. В следующем примере показано, как «разветвление» поля, копируя то же исходное поле в два разных поля индекса:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> В поиске Azure для устранения расхождений между именами полей и функций в сопоставлениях полей используется сравнение без учета регистра. Это удобно (допускаются ошибки в выборе регистра), но означает, что источник данных или индекс не может содержать поля, которые различаются только регистром.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Сопоставьте поля с помощью пакета SDK для .NET

Сопоставления полей определяются в пакете SDK для .NET с помощью класса [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) , содержащего свойства `SourceFieldName` и `TargetFieldName`, и необязательной `MappingFunction` ссылки.

Можно указать сопоставления полей при конструировании индексатора или позже, задав `Indexer.FieldMappings` свойство напрямую.

В следующем C# примере задаются сопоставления полей при построении индексатора.

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

Функция сопоставления полей преобразует содержимое поля до его сохранения в индексе. В настоящее время поддерживаются следующие функции сопоставления:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [урлдекоде](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>Функция base64Encode

Выполняет *безопасное* кодирование строки входных данных в Base64. Входные данные должны быть в кодировке UTF-8.

#### <a name="example---document-key-lookup"></a>Пример. Поиск ключа документа

В ключе документа поиска Azure могут отображаться только надежные символы (так как клиенты должны уметь обращаться к документу с помощью [API поиска](https://docs.microsoft.com/rest/api/searchservice/lookup-document) ). Если исходное поле ключа содержит недопустимые символы URL-адреса, можно использовать `base64Encode` функцию, чтобы преобразовать ее во время индексирования.

При извлечении закодированного ключа во время поиска можно использовать `base64Decode` функцию для получения исходного значения ключа и использовать его для получения исходного документа.

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

Если не включить свойство Parameters для функции сопоставления, по умолчанию используется значение `{"useHttpServerUtilityUrlTokenEncode" : true}`.

Поиск Azure поддерживает две различные кодировки Base64. При кодировании и декодировании одного и того же поля следует использовать одни и те же параметры. Дополнительные сведения см. в разделе [Параметры кодирования Base64](#base64details) , чтобы решить, какие параметры использовать.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>Функция base64Decode

Выполняет декодирование входной строки в Base64. Предполагается, что входные данные представляют собой зашифрованную строку в кодировке Base64.

#### <a name="example---decode-blob-metadata-or-urls"></a>Пример. декодирование метаданных BLOB-объекта или URL-адресов

Исходные данные могут содержать строки в кодировке Base64, например строки метаданных больших двоичных объектов или URL-адреса, которые должны быть включены в поиск в виде обычного текста. `base64Decode` Функцию можно использовать для преобразования закодированных данных обратно в обычные строки при заполнении индекса поиска.

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

Если не указать свойство Parameters, по умолчанию используется значение `{"useHttpServerUtilityUrlTokenEncode" : true}`.

Поиск Azure поддерживает две различные кодировки Base64. При кодировании и декодировании одного и того же поля следует использовать одни и те же параметры. Дополнительные сведения см. в разделе [Параметры кодирования Base64](#base64details) , чтобы решить, какие параметры использовать.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>параметры кодировки Base64

Поиск Azure поддерживает две различные кодировки Base64: **Токен URL-адреса HttpServerUtility**и **URL-шифрование в кодировке Base64 без заполнения**. Строка, которая кодируется в кодировке Base64 во время индексирования, должна быть впоследствии декодирована с использованием одних и тех же параметров кодировки, иначе результат не будет совпадать с исходным.

Если параметры `useHttpServerUtilityUrlTokenEncode` или `useHttpServerUtilityUrlTokenDecode` для кодирования и декодирования соответственно имеют значение `true`, то `base64Encode` ведет себя как [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) и `base64Decode` ведет себя как [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Если вы не используете полную .NET Framework (то есть вы используете .NET Core или другую платформу) для создания значений ключей для эмуляции поведения поиска Azure, то следует установить `useHttpServerUtilityUrlTokenEncode` и `useHttpServerUtilityUrlTokenDecode` в `false`. В зависимости от используемой библиотеки функции кодирования и декодирования Base64 могут отличаться от функций, используемых службой поиска Azure.

В следующей таблице сравниваются различные кодировки Base64 строки `00>00?00`. Чтобы определить требуемую дополнительную обработку (при наличии) для функций Base64, примените функцию кодировки библиотеки в строке `00>00?00` и сравните выходные данные с ожидаемыми выходными данными `MDA-MDA_MDA`.

| кодировка | Выводные данные кодировки Base64 | Дополнительная обработка после кодирования библиотеки | Дополнительная обработка перед кодированием библиотеки |
| --- | --- | --- | --- |
| Base64 с заполнением | `MDA+MDA/MDA=` | Использовать безопасные знаки URL-адреса и удалить заполнение | Использовать стандартные знаки Base64 и добавить заполнение |
| Base64 без заполнения | `MDA+MDA/MDA` | Использовать безопасные знаки URL-адреса | Использовать стандартные знаки Base64 |
| Безопасное кодирование строки вводных данных в Base64 с заполнением | `MDA-MDA_MDA=` | Удаление заполнения | Добавление заполнения |
| Безопасное кодирование строки вводных данных в Base64 без заполнения | `MDA-MDA_MDA` | Отсутствуют | Отсутствуют |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>Функция Екстракттокенатпоситион

Разбивает поле строки, используя заданный разделитель, и выбирает маркер из указанной позиции в результате разбиения.

Эта функция использует следующие параметры:

* `delimiter`: строка, которая используемая как разделитель при разбиении входной строки.
* `position`: целочисленная нулевая позиция маркера, которую нужно выбрать после разбиения входной строки.

Например, если при входных данных `Jane Doe` параметр `delimiter` имеет значение `" "` (пробел), а параметр `position` — значение 0, то результатом будет `Jane`; если параметр `position` имеет значение 1, то результатом будет `Doe`. Если позиция ссылается на несуществующий токен, выдается ошибка.

#### <a name="example---extract-a-name"></a>Пример. Извлечение имени

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

### <a name="jsonarraytostringcollection-function"></a>Функция Жсонаррайтострингколлектион

Преобразует строку, отформатированную как массив строк JSON, в массив строк, который можно использовать для заполнения поля `Collection(Edm.String)` в индексе.

Например, если исходной строкой является `["red", "white", "blue"]`, то целевое поле типа `Collection(Edm.String)` будет заполнено следующими тремя значениями: `red`, `white` и `blue`. Если входные значения нельзя проанализировать как массивы строк JSON, выдается ошибка.

#### <a name="example---populate-collection-from-relational-data"></a>Пример. заполнение коллекции реляционными данными

База данных SQL Azure не имеет встроенного типа данных, который естественным образом сопоставляется с `Collection(Edm.String)` полями в службе поиска Azure. Чтобы заполнить поля коллекции строк, можно предварительно обработать исходные данные в виде массива строк JSON, а затем использовать `jsonArrayToStringCollection` функцию Mapping.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

Подробный пример преобразования реляционных данных в поля коллекции индексов см. в разделе [моделирование реляционных данных](search-example-adventureworks-modeling.md).

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>Функция urlEncode

Эта функция может использоваться для кодирования строки, чтобы она была «защищена по URL-адресу». При использовании со строкой, содержащей символы, недопустимые в URL-адресе, эта функция преобразует эти "ненадежные" символы в эквиваленты символьных сущностей. Эта функция использует формат кодировки UTF-8.

#### <a name="example---document-key-lookup"></a>Пример. Поиск ключа документа

`urlEncode`можно использовать в качестве альтернативы `base64Encode` функции, если требуется преобразовать только незащищенные символы URL-адреса и оставить другие символы как есть.

Например, входная строка имеет `<hello>` вид, а целевое поле типа `(Edm.String)` будет заполнено значением`%3chello%3e`

При извлечении закодированного ключа во время поиска можно использовать `urlDecode` функцию для получения исходного значения ключа и использовать его для получения исходного документа.

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

 ### <a name="urldecode-function"></a>Функция Урлдекоде

 Эта функция преобразует строку в кодировке URL в декодированную строку, используя формат кодирования UTF-8.

 ### <a name="example---decode-blob-metadata"></a>Пример. декодирование метаданных BLOB-объекта

 Некоторые клиенты службы хранилища Azure автоматически кодируются метаданные BLOB-объектов, если содержат символы, отличные от ASCII. Однако если вы хотите сделать такие метаданные поддерживающими Поиск (в виде обычного текста), можно использовать `urlDecode` функцию для преобразования закодированных данных обратно в обычные строки при заполнении индекса поиска.

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