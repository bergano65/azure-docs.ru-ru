---
title: Перечислите коллекции restorable в Azure Cosmos DB MongoDB API — REST API
description: Отображение канала событий всех изменений, выполненных во всех Azure Cosmos DB коллекциях MongoDB в определенной базе данных. Это помогает в ситуации, когда контейнер был случайно удален.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 83e1c7c27e8c5d179c4ec6aa4ba64b3367294a48
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527939"
---
# <a name="list-restorable-collections-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Перечисление коллекций restorable в API Azure Cosmos DB для MongoDB с помощью REST API

Отображение канала событий всех изменений, выполненных на всех Azure Cosmos DB API для коллекций MongoDB в определенной базе данных. Это помогает в ситуации, когда контейнер был случайно удален. Для этого API требуется `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` разрешение

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview
```

С использованием необязательных параметров:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid={restorableMongodbDatabaseRid}
```

## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Имя | В | Обязательно | Тип | Описание |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |строка| Идентификатор GUID instanceId учетной записи базы данных restorable. |
| **расположение** | path | True | строка| Azure Cosmos DB регион с пробелами между словами и каждым словом в прописной буквы. |
| **subscriptionId** | path | True | строка| Идентификатор целевой подписки. |
| **версия API** | query | True | строка | Версия API, используемая для этой операции. |
| **рестораблемонгодбдатабасерид** | query | |строка| Идентификатор ресурса Azure Cosmos DB API для базы данных MongoDB. |

## <a name="responses"></a>Ответы

| Имя | Тип | Описание |
| --- | --- | --- |
| 200 ОК | [рестораблемонгодбколлектионслистресулт](#restorablemongodbcollectionslistresult)| Операция выполнена успешно. |
| Другие коды состояния | [дефаултеррорреспонсе](#defaulterrorresponse)| Ошибочный ответ, описывающий причину сбоя операции.|

## <a name="examples"></a>Примеры

### <a name="cosmosdbrestorablemongodbcollectionlist"></a>космосдбрестораблемонгодбколлектионлист

**Пример запроса**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid=PD5DALigDgw=
```

**Пример ответа**

Код состояния: 200.

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbCollections",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Collection1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Определения

|Определение  | Описание|
| --- | --- |
| [дефаултеррорреспонсе](#defaulterrorresponse) | Ошибочный ответ от службы. |
| [ErrorResponse](#errorresponse) | Ошибочный ответ. |
| [OperationType](#operationtype) | Перечисление для указания типа операции события. |
| [Ресурс](#resource) | Ресурс для события коллекции Azure Cosmos DB API для MongoDB |
| [рестораблемонгодбколлектионжетресулт](#restorablemongodbcollectiongetresult) | Azure Cosmos DB API для события сбора MongoDB |
| [рестораблемонгодбколлектионпропертиес](#restorablemongodbcollectionproperties) | Свойства события Azure Cosmos DB коллекции API для MongoDB |
| [рестораблемонгодбколлектионслистресулт](#restorablemongodbcollectionslistresult) | Ответ операции списка, содержащий события коллекции и их свойства. |

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
| Создание |строка|событие создания коллекции|
| DELETE |строка|событие удаления коллекции|
| Заменить |строка|событие изменения коллекции|

### <a name="resource"></a><a id="resource"></a>Ресурсов

Ресурс события Azure Cosmos DB MongoDB Collection

| **имя**; | **Тип** | **Описание** |
| --- | --- | --- |
| _rid |строка| Свойство, созданное системой. Уникальный идентификатор. |
| eventTimestamp |строка| Время, когда произошло данное событие сбора. |
| "тип_операции" |[OperationType](#operationtype)|  Тип операции этого события коллекции. |
| ownerId |строка| Имя коллекции MongoDB.|
| овнерресаурцеид |строка| Идентификатор ресурса коллекции MongoDB. |

### <a name="restorablemongodbcollectiongetresult"></a><a id="restorablemongodbcollectiongetresult"></a>рестораблемонгодбколлектионжетресулт

Событие сбора Azure Cosmos DB MongoDB

| **имя**; | **Тип** | **Описание** |
| --- | --- | --- |
| ID |строка| Уникальный идентификатор ресурса Azure Resource Manager ресурса. |
| name |строка| Имя ресурса диспетчер ресурсов. |
| properties |[рестораблемонгодбколлектионпропертиес](#restorablemongodbcollectionproperties)| Свойства события коллекции. |
| type |строка| Тип ресурса Azure. |

### <a name="restorablemongodbcollectionproperties"></a><a id="restorablemongodbcollectionproperties"></a>рестораблемонгодбколлектионпропертиес

Свойства события Azure Cosmos DB MongoDB Collection

| **имя**; | **Тип** | **Описание** |
| --- | --- | --- |
| resource | [Ресурс](#resource)| Ресурс для события коллекции Azure Cosmos DB API для MongoDB |

### <a name="restorablemongodbcollectionslistresult"></a><a id="restorablemongodbcollectionslistresult"></a>рестораблемонгодбколлектионслистресулт

Ответ операции списка, содержащий события коллекции и их свойства.

| **имя**; | **Тип** | **Описание** |
| --- | --- | --- |
| значение |[Рестораблемонгодбколлектионжетресулт](#restorablemongodbcollectiongetresult)[]| Список Azure Cosmos DB API для событий сбора MongoDB и их свойств. |

## <a name="next-steps"></a>Следующие шаги

* [Перечислите базы данных restorable](restorable-mongodb-databases-list.md)  в API Azure Cosmos DB для MongoDB с помощью REST API.
* [Модель ресурсов](continuous-backup-restore-resource-model.md) режима непрерывного резервного копирования.