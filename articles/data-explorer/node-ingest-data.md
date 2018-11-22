---
title: Краткое руководство. Прием данных с помощью библиотеки Node в Azure Data Explorer
description: В этом кратком руководстве вы узнаете, как принимать (загружать) данные в Azure Data Explorer с помощью Node.js.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 10/25/2018
ms.openlocfilehash: fa322ee685d09717ac5b98398d4d1d61de2be1e9
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706642"
---
# <a name="quickstart-ingest-data-using-the-azure-data-explorer-node-library"></a>Краткое руководство. Прием данных с помощью библиотеки Node в Azure Data Explorer

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. Azure Data Explorer предоставляет две клиентские библиотеки для Node: [библиотеку приема](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-ingest) и [библиотеку данных](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-data). Они позволяют принимать (загружать) данные в кластер и запрашивать данные из кода. В этом кратком руководстве вы создадите таблицу и сопоставление данных в кластере тестирования. Затем вы поставите в очередь прием данных в кластер и проверите результаты.

Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Кроме подписки Azure, для прохождения этого краткого курса необходимо следующее:

* [Тестовый кластер и база данных](create-cluster-database-portal.md)

* [Node.js](https://nodejs.org/en/download/) на компьютере, где ведется разработка.

## <a name="install-the-data-and-ingest-libraries"></a>Установка библиотек данных и приема

Установите *azure-kusto-ingest* и *azure-kusto-data*.

```bash
npm i --save azure-kusto-ingest azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>Добавление операторов и констант импорта

Импорт классов из библиотек

```javascript
const KustoClient = require('azure-kusto-data').KustoClient;
const KustoIngestClient = require('azure-kusto-ingest').KustoIngestClient;
const KustoConnectionStringBuilder = require('azure-kusto-ingest').KustoConnectionStringBuilder;
```

Чтобы проверить подлинность приложения, Azure Data Explorer использует идентификатор клиента Azure Active Directory. Чтобы найти идентификатор клиента, выполните инструкции в [этом разделе по клиенту Office 365](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id).

Задайте значения для свойств `authorityId`, `kustoUri`, `kustoIngestUri` и `kustoDatabase` перед выполнением этого кода.

```javascript
const authorityId = "<TenantId>";
const kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
const kustoIngestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
const kustoDatabase  = "<DatabaseName>"
```

Создайте строку подключения. В этом примере используется проверка подлинности устройства для доступа к кластеру. Можно также использовать сертификат приложения Azure Active Directory, ключ приложения, имя пользователя и пароль.

Целевую таблицу и сопоставление вы создадите позднее.

```javascript
const kcsbIngest = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoIngestUri, authorityId);

const kcsbData = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoUri, authorityId);

const destTable = "StormEvents";
const destTableMapping = "StormEvents_CSV_Mapping";
```

## <a name="set-source-file-information"></a>Определение данных исходного файла

Импортируйте дополнительные классы и задайте константы для исходного файла данных. В этом примере используется пример файла, размещенный в хранилище BLOB-объектов Azure. Набор демонстрационных данных **StormEvents** содержит данные о погоде из [Национальных центров Соединенных Штатов по экологической информации](https://www.ncdc.noaa.gov/stormevents/).

```javascript
from azure.storage.blob import BlockBlobService
from azure.kusto.ingest import KustoIngestClient, IngestionProperties, FileDescriptor, BlobDescriptor, DataFormat, ReportLevel, ReportMethod

const container = "samplefiles";
const account = "kustosamplefiles";
const sas = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D"
const filePath = "StormEvents.csv";
const fileSize = 64158321    # in bytes

const blobPath = `https://${account}.blob.core.windows.net/${container}/${filePath}${sas}";
```

## <a name="create-a-table-on-your-test-cluster"></a>Создание таблицы в тестовом кластере

Создайте таблицу, которая соответствует схеме данных в файле `StormEvents.csv`. При выполнении код запускает следующее сообщение: *Чтобы войти, воспользуйтесь браузером и откройте страницу https://microsoft.com/devicelogin. Введите код XXXXXXXXX для прохождения проверки подлинности*. Следуйте инструкциям по входу, а затем выполните следующий блок кода. Для выполнения последующих блоков кода, устанавливающих соединение, необходимо повторно выполнить вход.

```javascript
const kustoClient = new KustoClient(kcsbData);
const createTableCommand = ".create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)"

kustoClient.executeMgmt(kustoDatabase, createTableCommand, (err, results) => {
    console.log(result.primaryResults[0]);
});
```

## <a name="define-ingestion-mapping"></a>Определение сопоставления приема

Сопоставьте входящие данные CSV с именами столбцов и типами данных, которые использовались при создании таблицы.

```javascript
const createMappingCommand = `.create table StormEvents ingestion csv mapping ${destTableMapping} '[{\"Name\":\"StartTime\",\"datatype\":\"datetime\",\"Ordinal\":0}, {\"Name\":\"EndTime\",\"datatype\":\"datetime\",\"Ordinal\":1},{\"Name\":\"EpisodeId\",\"datatype\":\"int\",\"Ordinal\":2},{\"Name\":\"EventId\",\"datatype\":\"int\",\"Ordinal\":3},{\"Name\":\"State\",\"datatype\":\"string\",\"Ordinal\":4},{\"Name\":\"EventType\",\"datatype\":\"string\",\"Ordinal\":5},{\"Name\":\"InjuriesDirect\",\"datatype\":\"int\",\"Ordinal\":6},{\"Name\":\"InjuriesIndirect\",\"datatype\":\"int\",\"Ordinal\":7},{\"Name\":\"DeathsDirect\",\"datatype\":\"int\",\"Ordinal\":8},{\"Name\":\"DeathsIndirect\",\"datatype\":\"int\",\"Ordinal\":9},{\"Name\":\"DamageProperty\",\"datatype\":\"int\",\"Ordinal\":10},{\"Name\":\"DamageCrops\",\"datatype\":\"int\",\"Ordinal\":11},{\"Name\":\"Source\",\"datatype\":\"string\",\"Ordinal\":12},{\"Name\":\"BeginLocation\",\"datatype\":\"string\",\"Ordinal\":13},{\"Name\":\"EndLocation\",\"datatype\":\"string\",\"Ordinal\":14},{\"Name\":\"BeginLat\",\"datatype\":\"real\",\"Ordinal\":16},{\"Name\":\"BeginLon\",\"datatype\":\"real\",\"Ordinal\":17},{\"Name\":\"EndLat\",\"datatype\":\"real\",\"Ordinal\":18},{\"Name\":\"EndLon\",\"datatype\":\"real\",\"Ordinal\":19},{\"Name\":\"EpisodeNarrative\",\"datatype\":\"string\",\"Ordinal\":20},{\"Name\":\"EventNarrative\",\"datatype\":\"string\",\"Ordinal\":21},{\"Name\":\"StormSummary\",\"datatype\":\"dynamic\",\"Ordinal\":22}]'`;

kustoClient.executeMgmt(kustoDatabase, createMappingCommand, (err, results) => {
    console.log(result.primaryResults[0]);
});
```

## <a name="queue-a-message-for-ingestion"></a>Отправка сообщения в очередь на прием

Поставьте в очередь сообщение, чтобы получить данные из хранилища BLOB-объектов и получить эти данные в обозреватель данных Azure.

```javascript
const { DataFormat, JsonColumnMapping } =  require("azure-kusto-ingest").IngestionPropertiesEnums;
const { BlobDescriptor } = require("azure-kusto-ingest").Descriptors;
const ingestClient = new KustoIngestClient(kcsbIngest);


// All ingestion properties are documented here: https://docs.microsoft.com/azure/kusto/management/data-ingest#ingestion-properties
const ingestionProps  = new IngestionProperties(kustoDatabase, destTable, DataFormat.csv, null,destTableMapping, {'ignoreFirstRecord': 'true'});
const blobDesc = new BlobDescriptor(blobPath, 10);
ingestClient.ingestFromBlob(blobDesc,ingestionProps, (err) => {
    if (err) throw new Error(err);
});
```

## <a name="validate-that-table-contains-data"></a>Проверка наличия данных в таблице

Проверьте, что данные приняты в таблицу. Подождите 5–10 минут, пока поставленные в очередь прием запланирует прием и загрузку данных в обозреватель данных Azure. Затем выполните следующий код, чтобы получить количество записей в таблице `StormEvents`.

```javascript
const query = "StormEvents | count";

kustoClient.execute(kustoDatabse, query, (err, results) => {
    if (err) throw new Error(err);  
    console.log(results.primaryResults[0].toString());
});
```

## <a name="run-troubleshooting-queries"></a>Выполнение запросов по устранению неполадок

Войдите в [https://dataexplorer.azure.com](https://dataexplorer.azure.com) и подключитесь к кластеру. Выполните в своей базе данных следующую команду, чтобы проверить, не было ли в ней сбоев приема за последние четыре часа. Замените имя базы данных перед запуском.
    
```Kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

Выполните следующую команду, чтобы узнать состояние всех операций приема за последние четыре часа. Замените имя базы данных перед запуском.

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете работать с другими нашими краткими и подробными руководствами, сохраните созданные ресурсы. В противном случае выполните в своей базе данных следующую команду, чтобы очистить таблицу `StormEvents`.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Написание запросов](write-queries.md)
