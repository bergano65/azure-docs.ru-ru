---
title: Поиск больших двоичных объектов в виде обычного текста
titleSuffix: Azure Cognitive Search
description: Настройте индексатор поиска, чтобы извлечь обычный текст из больших двоичных объектов Azure для полнотекстового поиска в Когнитивный поиск Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 422346430e32ccb8745d5a5d829c5d61089a99c6
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430434"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Индексирование больших двоичных объектов с обычным текстом в Azure Когнитивный поиск

При использовании [индексатора больших двоичных объектов](search-howto-indexing-azure-blob-storage.md) для извлечения искомого текста для полнотекстового поиска можно вызвать различные режимы анализа для получения более эффективных результатов индексирования. По умолчанию индексатор анализирует содержимое большого двоичного объекта как один фрагмент текста. Однако если все большие двоичные объекты содержат обычный текст в одной кодировке, можно значительно повысить производительность индексирования с помощью `text` режима синтаксического анализа.

Режим анализа следует использовать в `text` следующих случаях:

+ Файл имеет тип TXT
+ Файлы имеют любой тип, но само содержимое является текстом (например, исходный код программы, HTML, XML и т. д.). Для файлов на языке метки все символы синтаксиса будут поступать как статический текст.

Вспомним, что индексаторы сериализуются в JSON. Содержимое всего текстового файла будет индексироваться в одном большом поле как `"content": "<file-contents>"` . Новые строки и инструкции по возврату выражаются в виде `\r\n\` .

Если требуется более детальный результат, примите во внимание следующие решения.

+ [`delimitedText`](search-howto-index-csv-blobs.md) режим синтаксического анализа, если источником является CSV-файл
+ [ `jsonArray` или `jsonLines` ](search-howto-index-json-blobs.md), если источником является JSON

Третий вариант для разбиения содержимого в нескольких частях требует расширенных функций в виде [обогащения искусственного интеллекта](cognitive-search-concept-intro.md). Он добавляет анализ, который определяет и присваивает фрагменты файла различным полям поиска. Вы можете найти полное или частичное решение с помощью [встроенных навыков](cognitive-search-predefined-skills.md), но более вероятное решение — это модель обучения, которая понимает ваше содержимое, в котором реализована пользовательская модель обучения, заключенная в [Пользовательский навык](cognitive-search-custom-skill-interface.md).

## <a name="set-up-plain-text-indexing"></a>Настройка индексирования в виде обычного текста

Для индексирования больших двоичных объектов с обычным текстом Создайте или обновите определение индексатора со `parsingMode` свойством Configuration в `text` в запросе на [Создание индексатора](/rest/api/searchservice/create-indexer) :

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "parsingMode" : "text" } }
}
```

По умолчанию предполагается кодировка `UTF-8`. Чтобы указать другую кодировку, используйте свойство конфигурации `encoding`: 

```http
{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
}
```

## <a name="request-example"></a>Пример запроса

Режимы анализа указываются в определении индексатора.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-plaintext-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```

## <a name="next-steps"></a>Дальнейшие действия

+ [Indexers in Azure Cognitive Search](search-indexer-overview.md) (Индексаторы в службе "Когнитивный поиск Azure")
+ [Настройка индексатора больших двоичных объектов](search-howto-indexing-azure-blob-storage.md)
+ [Общие сведения об индексировании BLOB-объектов](search-blob-storage-integration.md)