---
title: Перечисление баз данных API SQL restorable в Azure Cosmos DB с помощью REST API
description: Отображение веб-канала событий всех изменений, выполненных во всех Azure Cosmos DB базах данных SQL в учетной записи restorable. Это помогает в ситуации, когда база данных была случайно удалена для получения времени удаления.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 218a39a2bf8e9269e43c4876c1654d94be886997
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539507"
---
# <a name="list-restorable-sql-api-databases-in-azure-cosmos-db-using-rest-api"></a>Перечисление баз данных API SQL restorable в Azure Cosmos DB с помощью REST API

> [!IMPORTANT]
> Функция восстановления на момент времени (режим непрерывного резервного копирования) для Azure Cosmos DB в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Отображение веб-канала событий всех изменений, выполненных во всех Azure Cosmos DB базах данных SQL в учетной записи restorable. Это помогает в ситуации, когда база данных была случайно удалена для получения времени удаления. Для этого API требуется `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` разрешение

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlDatabases?api-version=2020-06-01-preview
```

## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Имя | В | Обязательно | Тип | Описание |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |строка| Идентификатор GUID instanceId учетной записи базы данных restorable. |
| **расположение** | path | True | строка| Azure Cosmos DB регион с пробелами между словами и каждым словом в прописной буквы. |
| **subscriptionId** | path | True | строка| Идентификатор целевой подписки. |
| **версия API** | query | True | строка | Версия API, используемая для этой операции. |

## <a name="responses"></a>Ответы

| Имя | Тип | Описание |
| --- | --- | --- |
| 200 ОК | [рестораблесклдатабасеслистресулт](#restorablesqldatabaseslistresult)| Операция выполнена успешно. |
| Другие коды состояния | [дефаултеррорреспонсе](#defaulterrorresponse)| Ошибочный ответ, описывающий причину сбоя операции. |

## <a name="examples"></a>Примеры

### <a name="cosmosdbrestorablesqldatabaselist"></a>космосдбрестораблесклдатабаселист

**Пример запроса**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases?api-version=2020-06-01-preview
```

**Пример ответа**

Код состояния: 200.

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableSqlDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "operationType": "Create",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/"
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/",
            "_ts": 1599075903
          },
          "operationType": "Delete"
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "properties": {
        "resource": {
          "_rid": "aXFqUQAAAA==",
          "eventTimestamp": "2020-09-02T19:53:15Z",
          "ownerId": "Database2",
          "ownerResourceId": "0SziSg==",
          "database": {
            "id": "Database2",
            "_rid": "0SziSg==",
            "_self": "dbs/0SziSg==/",
            "_etag": "\"0000ca0a-0000-0700-0000-5f4ff82b0000\"",
            "_colls": "colls/",
            "_users": "users/"
          },
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Определения

| Определение | Описание | | --- || --- | | [База данных](#database) | Azure Cosmos DB объекта ресурсов базы данных SQL | | [Дефаултеррорреспонсе](#defaulterrorresponse) | Ошибочный ответ от службы. | | [ErrorResponse](#errorresponse) | Ошибочный ответ. | | [OperationType](#operationtype) | Перечисление для указания типа операции события. | | [Ресурс](#resource) | Ресурс Azure Cosmos DB события базы данных SQL | | [Рестораблесклдатабасежетресулт](#restorablesqldatabasegetresult) | Событие базы данных SQL Azure Cosmos DB | | [Рестораблесклдатабасепропертиес](#restorablesqldatabaseproperties) | Свойства события базы данных SQL Azure Cosmos DB | | [Рестораблесклдатабасеслистресулт](#restorablesqldatabaseslistresult) | Ответ операции списка, который содержит события базы данных SQL и их свойства. |

### <a name="database"></a><a id="database"></a>СУБД

Объект ресурса базы данных SQL Azure Cosmos DB

| **Имя**  |  **Тип**  |  **Описание** | | --- || --- | ---| | _colls | строка | Созданное системой свойство, закоторое указывает путь к ресурсу коллекций с адресацией. | | _etag | строка | Созданное системой свойство, представляющее тег eTag ресурса, необходимый для управления оптимистичным параллелизмом. | | _rid | строка | Свойство, созданное системой. Уникальный идентификатор. | | _self | строка | Созданное системой свойство, указывающее путь к ресурсу базы данных с адресацией. | | _ts | | Созданное системой свойство, которое обозначает метку времени последнего обновления ресурса. | | _users | строка | Созданное системой свойство, указывающее путь к ресурсу пользователя. | | ID | строка | Имя базы данных SQL Azure Cosmos DB |

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
| Создание |строка|событие создания базы данных|
| DELETE |строка|событие удаления базы данных|
| Заменить |строка|событие изменения базы данных|
| системоператион |строка|событие изменения базы данных, активируемое системой. Это событие не инициируется пользователем|

### <a name="resource"></a><a id="resource"></a>Ресурсов

Ресурс события базы данных SQL Azure Cosmos DB

| **имя**; | **Тип** | **Описание** |
| --- | --- | --- |
| _rid |строка| Свойство, созданное системой. Уникальный идентификатор. |
| База данных |[База данных](#database)| Объект ресурса базы данных SQL Azure Cosmos DB |
| eventTimestamp |строка| Время, когда произошло данное событие базы данных. |
| "тип_операции" |[OperationType](#operationtype)| Тип операции этого события базы данных. |
| ownerId |строка| Имя базы данных SQL. |
| овнерресаурцеид |строка| Идентификатор ресурса базы данных SQL. |

### <a name="restorablesqldatabasegetresult"></a><a id="restorablesqldatabasegetresult"></a>рестораблесклдатабасежетресулт

Событие базы данных SQL Azure Cosmos DB

| **имя**; | **Тип** | **Описание** |
| --- | --- | --- |
| ID |строка| Уникальный идентификатор ресурса Azure Resource Manager ресурса. |
| name |строка| Имя ресурса Azure Resource Manager. |
| properties | [рестораблесклдатабасепропертиес](#restorablesqldatabaseproperties)| Свойства события базы данных SQL. |
| type |строка| Тип ресурса Azure. |

### <a name="restorablesqldatabaseproperties"></a><a id="restorablesqldatabaseproperties"></a>рестораблесклдатабасепропертиес

Свойства события базы данных SQL Azure Cosmos DB

| **имя**; | **Тип** | **Описание** |
| --- | --- | --- |
| resource |[Ресурс](#resource)| Ресурс события базы данных SQL Azure Cosmos DB |

### <a name="restorablesqldatabaseslistresult"></a><a id="restorablesqldatabaseslistresult"></a>рестораблесклдатабасеслистресулт

Ответ операции списка, который содержит события базы данных SQL и их свойства.

| **имя**; | **Тип** | **Описание** |
| --- | --- | --- |
| значение |[Рестораблесклдатабасежетресулт](#restorablesqldatabasegetresult)[]| Список событий базы данных SQL и их свойств. |

## <a name="next-steps"></a>Следующие шаги

* [Перечислите контейнеры restorable](restorable-sql-containers-list.md) в Azure Cosmos DB API SQL с помощью REST API.
* [Модель ресурсов](continuous-backup-restore-resource-model.md) режима непрерывного резервного копирования.