---
title: Устранение проблем общие проблемы индекса поиска
titleSuffix: Azure Cognitive Search
description: Исправление ошибок и распространенных проблем с индексаторами в Azure Cognitive Search, включая подключение к источнику данных, брандмауэр и недостающие документы.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1e3692920c35a6965a23c0305aeeebfc80505d85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190931"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Устранение проблем с распространенными проблемами индекса в Azure Cognitive Search

Индексаторы могут столкнуться с рядом проблем при индексации данных в Azure Cognitive Search. Эти проблемы можно разделить на несколько основных категорий:

* [Подключение к источнику данных или другим ресурсам](#connection-errors)
* [обработка документов](#document-processing-errors);
* [прием документов в индекс](#index-errors).

## <a name="connection-errors"></a>Ошибки подключения

> [!NOTE]
> Индексы имеют ограниченную поддержку доступа к источникам данных и другим ресурсам, которые защищены механизмами сетевой безопасности Azure. В настоящее время индексаторы могут получить доступ к источникам данных только через соответствующие механизмы ограничения диапазона IP-адресов или правила NSG, когда это применимо. Подробная информация о доступе к каждому поддерживаемому источнику данных приведена ниже.
>
> Вы можете узнать IP-адрес вашего поискового сервиса, повысив его полностью квалифицированное доменное имя (например, ). `<your-search-service-name>.search.windows.net`
>
> Вы можете узнать диапазон `AzureCognitiveSearch` [IP-адресов сервисного тега,](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) используя [загружаемые файлы JSON](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) или через [Service Tag Discovery API.](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview) Диапазон IP-адресов обновляется еженедельно.

### <a name="configure-firewall-rules"></a>Настройка правил брандмауэра

Azure Storage, CosmosDB и Azure S'L обеспечивают настраиваемый брандмауэр. Включение брандмауэра нельзя связать с одним конкретным сообщением об ошибке. Как правило, ошибки брандмауэра являются общими и выглядят как `The remote server returned an error: (403) Forbidden` или `Credentials provided in the connection string are invalid or have expired`.

Существует 2 варианта предоставления индексам доступа к этим ресурсам в таком случае:

* Отпустите брандмауэр, разрешив доступ от **всех сетей** (если это возможно).
* Кроме того, можно разрешить доступ к IP-адресу службы поиска `AzureCognitiveSearch` и диапазону [услуг](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) IP-адреса в правилах брандмауэра вашего ресурса (ограничение диапазона IP-адресов).

Подробную информацию для настройки ограничений диапазона IP-адресов для каждого типа источника данных можно найти по следующим ссылкам:

* [Хранилище Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Cosmos DB](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Azure SQL;](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)

**Ограничение**: Как указано в вышеупомянутой документации для Azure Storage, ограничения диапазона IP-адресов будут работать только в том случае, если служба поиска и учетная запись хранилища находятся в разных регионах.

Функции Azure (которые могут быть использованы в качестве [навыка Custom Web Api)](cognitive-search-custom-skill-web-api.md)также поддерживают [ограничения IP-адресов.](https://docs.microsoft.com/azure/azure-functions/ip-addresses#ip-address-restrictions) Список IP-адресов для настройки будет IP-адрес вашей службы поиска и IP-адреса диапазон `AzureCognitiveSearch` обслуживания тега.

Подробная информация о доступе к данным на сервере S'L на VM Azure приведена [здесь](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

### <a name="configure-network-security-group-nsg-rules"></a>Настройка правил сетевой безопасности (NSG)

При доступе к данным в управляемом экземпляре S'L или при использовании ВМ Azure в качестве веб-сервиса URI для [навыков Custom Web Api](cognitive-search-custom-skill-web-api.md)клиенты не должны беспокоиться о конкретных IP-адресах.

В таких случаях экземпляр Azure VM или управляемый экземпляр S'L может быть настроен для устройства виртуальной сети. Затем можно настроить группу сетевой безопасности для фильтрации типа сетевого трафика, который может проникать в и из виртуальных сетевых подсетей и сетевых интерфейсов.

Тег `AzureCognitiveSearch` службы может быть непосредственно использован в входящих [правилах NSG](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#work-with-security-rules) без необходимости искать диапазон IP-адресов.

Более подробная информация о доступе к данным приведена в приведенном [в данном](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md) случае экземпляре

### <a name="cosmosdb-indexing-isnt-enabled"></a>CosmosDB "Индексирование" не включено

Azure Cognitive Search имеет неявную зависимость от индексирования Cosmos DB. При отключении автоматической индексации в Cosmos DB Azure Cognitive Search возвращает успешное состояние, но не индексирует содержимое контейнера. Процессы проверки параметров и включения индексирования описаны в статье [Управление индексированием в Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal).

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

* Документ содержит только отсканированные изображения. Большие двоичные объекты в формате PDF, в которых есть только нетекстовое содержимое, например отсканированные изображения (JPG), не дают никаких результатов в стандартном конвейере индексирования BLOB-объектов. Если у вас есть содержимое изображения с текстовыми элементами, вы можете использовать [когнитивный поиск,](cognitive-search-concept-image-scenarios.md) чтобы найти и извлечь текст.
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
* Нужный документ исключается условиями [запроса](/rest/api/searchservice/create-data-source), который указан для этого источника данных. Индексаторы не могут индексировать документы, не являющихся частью источника данных.
* [Полевые картографы](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) или [обогащение ИИ](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) изменили документ, и он выглядит иначе, чем вы ожидаете.
* Используйте [API поиска документа](https://docs.microsoft.com/rest/api/searchservice/lookup-document), чтобы найти нужный документ.
