---
title: Устранение распространенных проблем с индексатором поиска
titleSuffix: Azure Cognitive Search
description: Устранение ошибок и распространенных проблем с индексаторами в Когнитивный поиск Azure, включая подключение к источнику данных, брандмауэр и отсутствующие документы.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c5a16d957f1e0414f92d0cc03442d88d438e4c92
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793632"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Устранение распространенных проблем с индексатором в Azure Когнитивный поиск

Индексаторы могут выполнять ряд проблем при индексировании данных в Azure Когнитивный поиск. Эти проблемы можно разделить на несколько основных категорий:

* [подключение к источнику данных](#data-source-connection-errors);
* [обработка документов](#document-processing-errors);
* [прием документов в индекс](#index-errors).

## <a name="data-source-connection-errors"></a>Проблемы с подключением к источнику данных

### <a name="blob-storage"></a>Хранилище BLOB-объектов

#### <a name="storage-account-firewall"></a>Брандмауэр учетной записи хранения

Служба хранилища Azure предоставляет брандмауэр с возможностью настройки. По умолчанию брандмауэр отключен, поэтому Azure Когнитивный поиск может подключиться к вашей учетной записи хранения.

Включение брандмауэра нельзя связать с одним конкретным сообщением об ошибке. Обычно связанные с брандмауэром ошибки выглядят как `The remote server returned an error: (403) Forbidden`.

Режим брандмауэра можно проверить на [портале](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal). Единственное поддерживаемое решение — отключить брандмауэр, выбрав разрешить доступ из ["все сети"](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal).

Если у индексатора нет присоединенного набора навыков, вы _можете_ попытаться [Добавить исключение](https://docs.microsoft.com/azure/storage/common/storage-network-security#managing-ip-network-rules) для IP-адресов службы поиска. Однако этот сценарий не поддерживается и не гарантирует работу.

IP-адрес службы поиска можно узнать с помощью команды ping по полному доменному имени (`<your-search-service-name>.search.windows.net`).

### <a name="cosmos-db"></a>База данных Cosmos

#### <a name="indexing-isnt-enabled"></a>Индексирование не включено

Когнитивный поиск Azure имеет неявную зависимость от Cosmos DB индексирования. Если вы отключаете автоматическое индексирование в Cosmos DB, Azure Когнитивный поиск возвращает состояние "успешно", но не может индексировать содержимое контейнера. Процессы проверки параметров и включения индексирования описаны в статье [Управление индексированием в Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal).

## <a name="document-processing-errors"></a>Ошибки обработки документов

### <a name="unprocessable-or-unsupported-documents"></a>Необрабатываемые или неподдерживаемые документы

Индексатор больших двоичных объектов [перечисляет форматы, которые поддерживаются явным образом](search-howto-indexing-azure-blob-storage.md#SupportedFormats). Иногда в контейнере хранилища BLOB-объектов могут встретиться неподдерживаемые документы. Также могут встречаться другие проблемы с этими документами. Чтобы индексатор не прекращал работу в таких случаях, [измените параметры конфигурации](search-howto-indexing-azure-blob-storage.md#DealingWithErrors):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Отсутствие содержимое в документе

Индексатор больших двоичных объектов [находит и извлекает текст из больших двоичных объектов в контейнере](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). При извлечении текста могут встречаться следующие проблемы.

* Документ содержит только отсканированные изображения. Большие двоичные объекты в формате PDF, в которых есть только нетекстовое содержимое, например отсканированные изображения (JPG), не дают никаких результатов в стандартном конвейере индексирования BLOB-объектов. Если изображение имеет содержимое с текстовыми элементами, для поиска и извлечения текста можно использовать «поиск с помощью [слов](cognitive-search-concept-image-scenarios.md) ».
* Индексатор больших двоичных объектов индексирует только метаданные. Чтобы извлечь содержимое, индексатор больших двоичных объектов должен быть настроен на [извлечение содержимого и метаданных](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Ошибки индекса

### <a name="missing-documents"></a>Отсутствие документов

Индексаторы находят документы из [источника данных](https://docs.microsoft.com/rest/api/searchservice/create-data-source). Иногда случается так, что в индексе отсутствует документ из источника данных, который должен быть индексирован. Такие ошибки могут возникать по нескольким распространенным причинам.

* Документ еще не был проиндексирован. Проверьте на портале, был ли индексатор успешно выполнен.
* Нужный документ обновился уже после выполнения индексатора. Если индексатор выполняется по [расписанию](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule), он через некоторое время будет выполнен снова и учтет нужный документ.
* Нужный документ исключается условиями [запроса](https://docs.microsoft.com/rest/api/searchservice/create-data-source#request-body-syntax), который указан для этого источника данных. Индексаторы не могут индексировать документы, не являющихся частью источника данных.
* [Сопоставления полей](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) или [обогащение искусственного интеллекта](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) изменили документ, и он будет отличаться от предполагаемого.
* Используйте [API поиска документа](https://docs.microsoft.com/rest/api/searchservice/lookup-document), чтобы найти нужный документ.
