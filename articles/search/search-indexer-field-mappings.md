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
ms.openlocfilehash: 3546e342b535a122ea4ed3f844cd5e28a76d551a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147797"
---
# <a name="field-mappings-and-transformations-using-azure-search-indexers"></a>Сопоставления полей и преобразований, использовании индексаторов поиска Azure

При использовании индексаторов поиска Azure, иногда можно обнаружить, что входные данные не вписываются в схему целевого индекса. В таких случаях можно использовать **сопоставления полей** для изменения формы данных в процессе индексирования.

Примеры ситуаций, в которых могут пригодиться сопоставления полей:

* Источник данных содержит поле с именем `_id`, но поиск Azure не разрешает имена полей, начинающиеся с символа подчеркивания. Сопоставление полей позволяет эффективно переименовать поле.
* Вы хотите заполнить несколько полей в индексе из тех же данных источника данных. Например может потребоваться применить разные анализаторы этим полям.
* Вам нужно заполнить Индексное поле с данными из нескольких источников данных и источники данных каждого используют различными именами полей.
* Данные необходимо шифровать или расшифровывать в кодировке Base64. Сопоставления полей поддерживают несколько **функций сопоставления**, включая шифрование и расшифровку в кодировке Base64.

> [!NOTE]
> Функции сопоставления полей индексаторов поиска Azure предоставляет простой способ сопоставления полей данных полей индекса, с помощью несколько параметров для преобразования данных. Более сложных данных может потребоваться предварительной обработки, чтобы изменить его форму в форму, которая легко индекса.
>
> Фабрики данных Microsoft Azure — это мощное Облачное решение для импорта и преобразования данных. Можно также написать код для преобразования исходных данных до индексации. Примеры кода, см. в разделе [моделирование реляционных данных](search-example-adventureworks-modeling.md) и [модели многоуровневой аспекты](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Настройка сопоставления полей

Сопоставление полей состоит из трех частей:

1. Параметр `sourceFieldName`— представляет поле в источнике данных. Это — обязательное свойство.
2. Необязательный параметр `targetFieldName`— представляет поле в индексе поиска. Если этот параметр опущен, используется такое же имя, как в источнике данных.
3. Необязательный параметр `mappingFunction`— позволяет преобразовывать данные, используя одну из нескольких предопределенных функций. Полный список функций представлен [ниже](#mappingFunctions).

Сопоставления полей добавляются к `fieldMappings` массив определение индексатора.

## <a name="map-fields-using-the-rest-api"></a>Сопоставление полей с помощью REST API

Можно добавить сопоставления полей при создании нового индексатора с использованием [Создание индексатора](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) запроса API. Вы можете управлять сопоставления полей для существующего индексатора с помощью [Update индексатор](https://docs.microsoft.com/rest/api/searchservice/update-indexer) запроса API.

Например Вот как сопоставить поля-источника с другим именем целевого поля:

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

Поля-источника можно ссылаться в несколько сопоставлений полей. Приведенный ниже показано, как «разветвления» полем, копирование одно и то же поле источника для двух полей другой индекс:

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

## <a name="map-fields-using-the-net-sdk"></a>Сопоставление полей с помощью пакета SDK для .NET

Определение сопоставления полей в пакет SDK для .NET с помощью [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) класс, который имеет свойства `SourceFieldName` и `TargetFieldName`и, при необходимости `MappingFunction` ссылки.

Сопоставления полей можно указать при создании индексатора, или более поздней версии путем непосредственной настройки `Indexer.FieldMappings` свойство.

Следующие C# пример задает сопоставления полей при создании индексатор.

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

Функция сопоставления полей преобразует значения поля перед его сохранением в индексе. В настоящее время поддерживаются следующие функции сопоставления:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>функция base64Encode

Выполняет *безопасное* кодирование строки входных данных в Base64. Входные данные должны быть в кодировке UTF-8.

#### <a name="example---document-key-lookup"></a>Пример: поиск ключа документа

Может отображаться только безопасные знаки URL-ключ документа поиска Azure в (так как клиенты должны иметь возможность обращаться документа с помощью [API поиска](https://docs.microsoft.com/rest/api/searchservice/lookup-document) ). Если исходное поле ключа содержит знаки, небезопасные для URL-адрес, вы можете использовать `base64Encode` функцию для преобразования во время индексирования.

При извлечении закодированного ключа во время поиска, можно использовать `base64Decode` функция, чтобы получить исходное значение ключа и использовать его для получения исходного документа.

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

Если вы не включаете свойства параметров для функции сопоставления, по умолчанию используется значение `{"useHttpServerUtilityUrlTokenEncode" : true}`.

Поиск Azure поддерживает две различные кодировки Base64. Следует использовать те же параметры, когда кодирования и декодирования то же поле. Дополнительные сведения см. в разделе [параметры кодировки base64](#base64details) Чтобы решить, какие параметры использовать.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>функция base64Decode

Выполняет декодирование входной строки в Base64. Входные данные предполагается, что *безопасное* строку в кодировке Base64.

#### <a name="example---decode-blob-metadata-or-urls"></a>Пример — декодирование метаданные большого двоичного объекта или URL-адреса

Источник данных может содержать строки в кодировке Base64, такими как строки метаданных большого двоичного объекта или URL-адреса, которые требуется сделать доступными для поиска в виде обычного текста. Можно использовать `base64Decode` функции, чтобы превратить зашифрованные данные обратно в обычные строки, при заполнении индекса поиска.

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

Если вы не включаете свойство parameters, по умолчанию используется значение `{"useHttpServerUtilityUrlTokenEncode" : true}`.

Поиск Azure поддерживает две различные кодировки Base64. Следует использовать те же параметры, когда кодирования и декодирования то же поле. Дополнительные сведения см. в разделе [параметры кодировки base64](#base64details) Чтобы решить, какие параметры использовать.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>Параметры кодировки Base64

Поиск Azure поддерживает две различные кодировки Base64: **Токен URL-адреса HttpServerUtility**, и **кодировку безопасное Base64 без заполнения**. Строка, которая является 64 разрядной кодировки во время индексирования должен быть декодирован позже с теми же параметрами кодирования, в противном случае результат не будет соответствовать исходной.

Если `useHttpServerUtilityUrlTokenEncode` или `useHttpServerUtilityUrlTokenDecode` для кодирования и декодирования соответственно присвоено `true`, затем `base64Encode` ведет себя как [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) и `base64Decode` ведет себя как [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Если вы не используете полную версию .NET Framework (то есть вы используете .NET Core или другой платформы) для получения значения ключей, чтобы эмулировать поведение службы поиска Azure, то следует задать `useHttpServerUtilityUrlTokenEncode` и `useHttpServerUtilityUrlTokenDecode` для `false`. В зависимости от используемой библиотеки base64 кодирования и декодирования функции могут отличаться от тех, которые используются службой поиска Azure.

В следующей таблице сравниваются различные кодировки Base64 строки `00>00?00`. Чтобы определить требуемую дополнительную обработку (при наличии) для функций Base64, примените функцию кодировки библиотеки в строке `00>00?00` и сравните выходные данные с ожидаемыми выходными данными `MDA-MDA_MDA`.

| Кодирование | Выводные данные кодировки Base64 | Дополнительная обработка после кодирования библиотеки | Дополнительная обработка перед кодированием библиотеки |
| --- | --- | --- | --- |
| Base64 с заполнением | `MDA+MDA/MDA=` | Использовать безопасные знаки URL-адреса и удалить заполнение | Использовать стандартные знаки Base64 и добавить заполнение |
| Base64 без заполнения | `MDA+MDA/MDA` | Использовать безопасные знаки URL-адреса | Использовать стандартные знаки Base64 |
| Безопасное кодирование строки вводных данных в Base64 с заполнением | `MDA-MDA_MDA=` | Удаление заполнения | Добавление заполнения |
| Безопасное кодирование строки вводных данных в Base64 без заполнения | `MDA-MDA_MDA` | Нет | Нет |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>функция extractTokenAtPosition

Разбивает поле строки, используя заданный разделитель, и выбирает маркер из указанной позиции в результате разбиения.

Эта функция использует следующие параметры:

* `delimiter`: строка, которая используемая как разделитель при разбиении входной строки.
* `position`: целочисленная нулевая позиция маркера, которую нужно выбрать после разбиения входной строки.

Например, если при входных данных `Jane Doe` параметр `delimiter` имеет значение `" "` (пробел), а параметр `position` — значение 0, то результатом будет `Jane`; если параметр `position` имеет значение 1, то результатом будет `Doe`. Если позиция ссылается на несуществующий токен, выдается ошибка.

#### <a name="example---extract-a-name"></a>Пример: извлечение имя

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

#### <a name="example---populate-collection-from-relational-data"></a>Пример - заполнение коллекции из реляционной базы данных

База данных SQL Azure не имеет встроенного типа данных, естественным образом сопоставляется `Collection(Edm.String)` поля в службе поиска Azure. Чтобы заполнить поля в коллекции строк, можно предварительно обработать исходные данные как массив строк JSON, а затем использовать `jsonArrayToStringCollection` функция сопоставления.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

Подробный пример преобразования реляционных данных в коллекции полей индекса, см. в разделе [моделирование реляционных данных](search-example-adventureworks-modeling.md).
