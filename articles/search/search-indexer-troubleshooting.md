---
title: Устранение распространенных неполадок с индексатором поиска в службе "Поиск Azure"
description: Сведения об устранении распространенных неполадок с индексаторами в службе "Поиск Azure", в том числе проблем с подключением к источнику данных, брандмауэром или отсутствующими документами.
author: mgottein
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: magottei
ms.custom: seodec2018
ms.openlocfilehash: 256a38320c9b3ca826ee9c12ac0a437957f988e2
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65539279"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-search"></a>Устранение распространенных неполадок с индексатором в Поиске Azure

Индексаторы могут встречать много разных проблем при индексировании данных в Поиск Azure. Эти проблемы можно разделить на несколько основных категорий:

* [подключение к источнику данных](#data-source-connection-errors);
* [обработка документов](#document-processing-errors);
* [прием документов в индекс](#index-errors).

## <a name="data-source-connection-errors"></a>Проблемы с подключением к источнику данных

### <a name="blob-storage"></a>Хранилище BLOB-объектов

#### <a name="storage-account-firewall"></a>Брандмауэр учетной записи хранения

Служба хранилища Azure предоставляет брандмауэр с возможностью настройки. По умолчанию брандмауэр отключен и не мешает Поиску Azure подключаться к учетной записи хранения.

Включение брандмауэра нельзя связать с одним конкретным сообщением об ошибке. Обычно связанные с брандмауэром ошибки выглядят как `The remote server returned an error: (403) Forbidden`.

Режим брандмауэра можно проверить на [портале](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal). Если он включен, проблему можно решить двумя способами.

1. Отключите брандмауэр, выбрав вариант разрешения доступа [Все сети](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal).
1. [Добавьте исключение](https://docs.microsoft.com/azure/storage/common/storage-network-security#managing-ip-network-rules) для IP-адреса службы поиска. Чтобы узнать этот IP-адрес, выполните следующую команду:

`nslookup <service name>.search.windows.net`

Исключения не работают для [когнитивный поиск](cognitive-search-concept-intro.md). В этом случае остается только одно решение — отключить брандмауэр.

### <a name="cosmos-db"></a>База данных Cosmos

#### <a name="indexing-isnt-enabled"></a>Индексирование не включено

Поиск Azure имеет неявную зависимость от индексирования Cosmos DB. Если вы отключите автоматическое индексирование в Cosmos DB, Поиск Azure сообщит об успешном выполнении операции, но не сможет индексировать содержимое контейнера. Процессы проверки параметров и включения индексирования описаны в статье [Управление индексированием в Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#manage-indexing-using-azure-portal).

## <a name="document-processing-errors"></a>Ошибки обработки документов

### <a name="unprocessable-or-unsupported-documents"></a>Необрабатываемые или неподдерживаемые документы

Индексатор больших двоичных объектов [перечисляет форматы, которые поддерживаются явным образом](search-howto-indexing-azure-blob-storage.md#supported-document-formats). Иногда в контейнере хранилища BLOB-объектов могут встретиться неподдерживаемые документы. Также могут встречаться другие проблемы с этими документами. Чтобы индексатор не прекращал работу в таких случаях, [измените параметры конфигурации](search-howto-indexing-azure-blob-storage.md#dealing-with-errors):

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

* Документ содержит только отсканированные изображения. Большие двоичные объекты в формате PDF, в которых есть только нетекстовое содержимое, например отсканированные изображения (JPG), не дают никаких результатов в стандартном конвейере индексирования BLOB-объектов. Если у вас есть образ содержимого с помощью текстовых элементов, можно использовать [когнитивный поиск](cognitive-search-concept-image-scenarios.md) для поиска и извлечения текста.
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
* [Сопоставление полей](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) или [когнитивный поиск](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) изменились документа и она выглядит иначе, чем ожидалось.
* Используйте [API поиска документа](https://docs.microsoft.com/rest/api/searchservice/lookup-document), чтобы найти нужный документ.
