---
title: Перечисление контейнеров API SQL restorable в Azure Cosmos DB с помощью REST API
description: Отображение канала событий всех изменений, выполненных для всех Azure Cosmos DB контейнеров SQL в определенной базе данных. Это помогает в ситуации, когда контейнер был случайно удален.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 90018d3e1b793575830ba34756ad685927612006
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527952"
---
# <a name="list-restorable-sql-api-containers-in-azure-cosmos-db-using-rest-api"></a>Перечисление контейнеров API SQL restorable в Azure Cosmos DB с помощью REST API

Отображение канала событий всех изменений, выполненных для всех Azure Cosmos DB контейнеров SQL в определенной базе данных. Это помогает в ситуации, когда контейнер был случайно удален. Для этого API требуется `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` разрешение

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview
```

С использованием необязательных параметров:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid={restorableSqlDatabaseRid}
```

## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Имя | В | Обязательно | Тип | Описание |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |строка| Идентификатор GUID instanceId учетной записи базы данных restorable. |
| **расположение** | path | True | строка| Azure Cosmos DB регион с пробелами между словами и каждым словом в прописной буквы. |
| **subscriptionId** | path | True | строка| Идентификатор целевой подписки. |
| **версия API** | query | True | строка | Версия API, используемая для этой операции. |
| **рестораблесклдатабасерид** | query | |строка| Идентификатор ресурса базы данных SQL. |

## <a name="responses"></a>Ответы

| Имя | Тип | Описание |
| --- | --- | --- |
| 200 ОК | [рестораблесклконтаинерслистресулт](#restorablesqlcontainerslistresult)| Операция выполнена успешно. |
| Другие коды состояния | [дефаултеррорреспонсе](#defaulterrorresponse)| Ошибочный ответ, описывающий причину сбоя операции. |

## <a name="examples"></a>Примеры

### <a name="cosmosdbrestorablesqlcontainerlist"></a>космосдбрестораблесклконтаинерлист

**Пример запроса**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid=3fu-hg==
```

**Пример ответа**

Код состояния: 200.

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00003e00-0000-0700-0000-5f85338a0000\""
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/e85298a1-c631-4726-825e-a7ca092e9098",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "e85298a1-c631-4726-825e-a7ca092e9098",
      "properties": {
        "resource": {
          "_rid": "PrArcgAAAA==",
          "eventTimestamp": "2020-10-13T05:03:27Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Replace",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "defaultTtl": 12345,
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00004400-0000-0700-0000-5f85351f0000\""
          }
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Определения

| Определение | Описание | | --- || --- | | [Контейнер](#container) | Объект ресурса контейнера SQL Azure Cosmos DB | | [Дефаултеррорреспонсе](#defaulterrorresponse) | Ошибочный ответ от службы. | | [ErrorResponse](#errorresponse) | Ошибочный ответ. | | [OperationType](#operationtype) | Перечисление для указания типа операции события. | | [Ресурс](#resource) | Ресурс события контейнера Azure Cosmos DB SQL | | [Рестораблесклконтаинержетресулт](#restorablesqlcontainergetresult) | Событие контейнера Azure Cosmos DB SQL | | [Рестораблесклконтаинерпропертиес](#restorablesqlcontainerproperties) | Свойства события контейнера SQL Azure Cosmos DB | | [Рестораблесклконтаинерслистресулт](#restorablesqlcontainerslistresult) | Ответ операции списка, который содержит события контейнера SQL и их свойства. |

### <a name="container"></a><a id="container"></a>Контейнер

Объект ресурса контейнера SQL Azure Cosmos DB

| **Имя**  |  **Тип**  |  **Описание** | | --- || --- | ---| | _etag | строка | Созданное системой свойство, представляющее тег eTag ресурса, необходимый для управления оптимистичным параллелизмом. | | _rid | строка | Свойство, созданное системой. Уникальный идентификатор. | | _self | строка | Созданное системой свойство, указывающее путь к ресурсу контейнера с адресацией. | | _ts | | Созданное системой свойство, которое обозначает метку времени последнего обновления ресурса. | | ID | строка | Имя контейнера Azure Cosmos DB SQL |

### <a name="defaulterrorresponse"></a><a id="defaulterrorresponse"></a>дефаултеррорреспонсе

Ошибочный ответ от службы.

| **имя**; | **Тип** | **Описание** |
| --- | --- | --- |
| Ошибка | [ErrorResponse](#errorresponse)| Ошибочный ответ. |

### <a name="errorresponse"></a><a id="errorresponse"></a>ErrorResponse

Ошибочный ответ.

| **имя**; | **Тип** | **Описание** |
| --- | --- | --- |
| code |строка| Код ошибки. |
| message |строка| Сообщение об ошибке, указывающее, почему произошел сбой операции. |

### <a name="operationtype"></a><a id="operationtype"></a>OperationType

Перечисление для указания типа операции события.

| **имя**; | **Тип** | **Описание** |
| --- | --- | --- |
| Создание |строка|событие создания контейнера|
| DELETE |строка|событие удаления контейнера|
| Заменить |строка|событие изменения контейнера|
| системоператион |строка|событие изменения контейнера, активируемое системой. Это событие не инициируется пользователем|

### <a name="resource"></a><a id="resource"></a>Ресурсов

Ресурс события контейнера Azure Cosmos DB SQL

| **имя**; | **Тип** | **Описание** |
| --- | --- | --- |
| _rid |строка| Свойство, созданное системой. Уникальный идентификатор. |
| контейнер |[Контейнер](#container)| Объект ресурса контейнера SQL Azure Cosmos DB |
| eventTimestamp |строка| Время, когда произошло это событие контейнера. |
| "тип_операции" |[OperationType](#operationtype)| Тип операции этого события контейнера. |
| ownerId |строка| Имя контейнера SQL. |
| овнерресаурцеид |строка| Идентификатор ресурса для контейнера SQL. |

### <a name="restorablesqlcontainergetresult"></a><a id="restorablesqlcontainergetresult"></a>рестораблесклконтаинержетресулт

Событие контейнера Azure Cosmos DB SQL

| **имя**; | **Тип** | **Описание** |
| --- | --- | ---
| ID |строка| Уникальный идентификатор ресурса Azure Resource Manager ресурса. |
| name |строка| Имя ресурса Azure Resource Manager. |
| properties |[рестораблесклконтаинерпропертиес](#restorablesqlcontainerproperties)| Свойства события контейнера SQL. |
| type |строка| Тип ресурса Azure. |

### <a name="restorablesqlcontainerproperties"></a><a id="restorablesqlcontainerproperties"></a>рестораблесклконтаинерпропертиес

Свойства события контейнера SQL Azure Cosmos DB

| **имя**; | **Тип** | **Описание** |
| --- | --- | --- |
| resource |[Ресурс](#resource)| Ресурс события контейнера Azure Cosmos DB SQL |

### <a name="restorablesqlcontainerslistresult"></a><a id="restorablesqlcontainerslistresult"></a>рестораблесклконтаинерслистресулт

Ответ операции списка, который содержит события контейнера SQL и их свойства.

| **имя**; | **Тип** | **Описание** |
| --- | --- | --- |
| значение |[Рестораблесклконтаинержетресулт](#restorablesqlcontainergetresult)[]| Список событий контейнера SQL и их свойств. |

## <a name="next-steps"></a>Следующие шаги

* [Перечислите базы данных restorable](restorable-mongodb-databases-list.md)  в API Azure Cosmos DB для MongoDB с помощью REST API.
* [Модель ресурсов](continuous-backup-restore-resource-model.md) режима непрерывного резервного копирования.