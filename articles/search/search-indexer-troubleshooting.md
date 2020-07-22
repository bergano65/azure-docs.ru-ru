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
ms.openlocfilehash: 79db94298d190f646393410ec73ba1a25bb48270
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85560387"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Устранение распространенных проблем с индексатором в Azure Когнитивный поиск

Индексаторы могут выполнять ряд проблем при индексировании данных в Azure Когнитивный поиск. Эти проблемы можно разделить на несколько основных категорий:

* [Подключение к источнику данных или другим ресурсам](#connection-errors)
* [обработка документов](#document-processing-errors);
* [прием документов в индекс](#index-errors).

## <a name="connection-errors"></a>Ошибки подключения

> [!NOTE]
> Индексаторы имеют ограниченную поддержку доступа к источникам данных и другим ресурсам, защищенным механизмами безопасности сети Azure. В настоящее время Индексаторы могут обращаться к источникам данных только через соответствующие механизмы ограничения диапазона IP-адресов или правила NSG, если применимо. Подробные сведения о доступе к каждому поддерживаемому источнику данных можно найти ниже.
>
> IP-адрес службы поиска можно узнать, обратившись к полному доменному имени (например, `<your-search-service-name>.search.windows.net` ).
>
> Диапазон IP-адресов для `AzureCognitiveSearch` [тега службы](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) можно узнать с помощью [загружаемых файлов JSON](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) или через [API обнаружения тегов служб](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview). Диапазон IP-адресов обновляется еженедельно.

### <a name="configure-firewall-rules"></a>Настройка правил брандмауэра

Служба хранилища Azure, CosmosDB и Azure SQL предоставляют настраиваемый брандмауэр. Включение брандмауэра нельзя связать с одним конкретным сообщением об ошибке. Как правило, ошибки брандмауэра являются универсальными и выглядят как `The remote server returned an error: (403) Forbidden` или `Credentials provided in the connection string are invalid or have expired` .

Существует два варианта предоставления индексаторам доступа к этим ресурсам в таком экземпляре:

* Отключите брандмауэр, разрешая доступ из **всех сетей** (если возможно).
* Кроме того, можно разрешить доступ для IP-адреса службы поиска и диапазона IP-адресов `AzureCognitiveSearch` [тега службы](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) в правилах брандмауэра ресурса (ограничение диапазона IP-адресов).

Сведения о настройке ограничений диапазона IP-адресов для каждого типа источника данных можно найти по следующим ссылкам:

* [Хранилище Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [База данных Cosmos](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)

**Ограничение**. как указано в документации по службе хранилища Azure, ограничения диапазона IP-адресов будут работать только в том случае, если служба поиска и учетная запись хранения находятся в разных регионах.

Функции Azure (которые могут использоваться в качестве [настраиваемых навыков веб-API](cognitive-search-custom-skill-web-api.md)) также поддерживают [ограничения IP-адресов](https://docs.microsoft.com/azure/azure-functions/ip-addresses#ip-address-restrictions). Список IP-адресов для настройки — это IP-адрес службы поиска и диапазон IP-адресов `AzureCognitiveSearch` тега службы.

Сведения о доступе к данным в SQL Server на виртуальной машине Azure приведены [здесь](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

### <a name="configure-network-security-group-nsg-rules"></a>Настройка правил группы безопасности сети (NSG)

При доступе к данным в управляемом экземпляре SQL или при использовании виртуальной машины Azure в качестве URI веб-службы для [настраиваемого навыка веб-API](cognitive-search-custom-skill-web-api.md)клиенты не должны беспокоиться об использовании конкретных IP-адресов.

В таких случаях виртуальную машину Azure или управляемый экземпляр SQL можно настроить так, чтобы они находились в виртуальной сети. После этого Группа безопасности сети может быть настроена для фильтрации типа сетевого трафика, который может передаваться в подсетях виртуальной сети и сетевых интерфейсов.

`AzureCognitiveSearch`Тег службы можно использовать непосредственно в [правилах NSG](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#work-with-security-rules) входящих подключений без необходимости поиска своего диапазона IP-адресов.

Дополнительные сведения о доступе к данным в управляемом экземпляре SQL см. [здесь](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md) .

### <a name="cosmosdb-indexing-isnt-enabled"></a>CosmosDB "индексирование" не включено

Когнитивный поиск Azure имеет неявную зависимость от Cosmos DB индексирования. Если вы отключаете автоматическое индексирование в Cosmos DB, Azure Когнитивный поиск возвращает состояние "успешно", но не может индексировать содержимое контейнера. Процессы проверки параметров и включения индексирования описаны в статье [Управление индексированием в Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal).

## <a name="document-processing-errors"></a>Ошибки обработки документов

### <a name="unprocessable-or-unsupported-documents"></a>Необрабатываемые или неподдерживаемые документы

Индексатор больших двоичных объектов [перечисляет форматы, которые поддерживаются явным образом](search-howto-indexing-azure-blob-storage.md#SupportedFormats). Иногда в контейнере хранилища BLOB-объектов могут встретиться неподдерживаемые документы. Также могут встречаться другие проблемы с этими документами. Чтобы индексатор не прекращал работу в таких случаях, [измените параметры конфигурации](search-howto-indexing-azure-blob-storage.md#DealingWithErrors):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
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
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
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
* Проверьте значение [отслеживания изменений](https://docs.microsoft.com/rest/api/searchservice/create-data-source#data-change-detection-policies) . Если значение верхнего предела является датой, заданной в будущем, то индексатор будет пропущен для всех документов, имеющих дату меньше этого значения. Состояние отслеживания изменений индексатора можно узнать с помощью полей "Инитиалтраккингстате" и "Финалтраккингстате" в [состоянии индексатора](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status#indexer-execution-result).
* Нужный документ обновился уже после выполнения индексатора. Если индексатор выполняется по [расписанию](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule), он через некоторое время будет выполнен снова и учтет нужный документ.
* Нужный документ исключается условиями [запроса](/rest/api/searchservice/create-data-source), который указан для этого источника данных. Индексаторы не могут индексировать документы, не являющихся частью источника данных.
* [Сопоставления полей](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) или [обогащение искусственного интеллекта](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) изменили документ, и он будет отличаться от предполагаемого.
* Используйте [API поиска документа](https://docs.microsoft.com/rest/api/searchservice/lookup-document), чтобы найти нужный документ.
