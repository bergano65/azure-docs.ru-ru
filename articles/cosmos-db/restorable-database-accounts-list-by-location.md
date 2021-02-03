---
title: Перечислить учетные записи базы данных restorable по расположению с помощью Azure Cosmos DB REST API
description: Список всех учетных записей базы данных restorable Azure Cosmos DB, доступных в рамках подписки и в регионе.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 2a3fbc1bb00c57c20436c19602c135f1917c6a60
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527901"
---
# <a name="list-restorable-database-accounts-by-location-using-azure-cosmos-db-rest-api"></a>Перечислить учетные записи базы данных restorable по расположению с помощью Azure Cosmos DB REST API

Список всех учетных записей базы данных restorable Azure Cosmos DB, доступных в рамках подписки и в регионе. Для этого вызова требуется `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` разрешение.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts?api-version=2020-06-01-preview

```

## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Имя | В | Обязательно | Тип | Описание |
| --- | --- | --- | --- | --- |
| **расположение** | path | True | строка| Azure Cosmos DB регион с пробелами между словами и каждым словом в прописной буквы. |
| **subscriptionId** | path | True | строка| Идентификатор целевой подписки. |
| **версия API** | query | True | строка | Версия API, используемая для этой операции. |

## <a name="responses"></a>Ответы

| Имя | Тип | Описание |
| --- | --- | --- |
| 200 ОК | [ресторабледатабасеаккаунтслистресулт](#restorabledatabaseaccountslistresult)| Операция выполнена успешно. |
| Другие коды состояния | [дефаултеррорреспонсе](#defaulterrorresponse)| Ошибочный ответ, описывающий причину сбоя операции. |

## <a name="examples"></a>Примеры

### <a name="cosmosdbdatabaseaccountlist"></a>космосдбдатабасеаккаунтлист

**Пример запроса**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts?api-version=2020-06-01-preview
```

**Пример ответа**

Код состояния: 200.

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "name": "d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "location": "West US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb1",
        "creationTime": "2020-04-11T21:56:15Z",
        "deletionTime": "2020-06-12T22:05:09Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "name": "4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "location": "West US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb2",
        "creationTime": "2020-05-01T08:05:18Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    }
  ]
}
```

## <a name="definitions"></a>Определения

|Определение  | Описание|
| --- | --- |
| [апитипе](#apitype) | Перечисление для указания типа API учетной записи базы данных restorable. |
| [дефаултеррорреспонсе](#defaulterrorresponse) | Ошибочный ответ от службы. |
| [ErrorResponse](#errorresponse) | Ошибочный ответ. |
| [ресторабледатабасеаккаунтжетресулт](#restorabledatabaseaccountgetresult) | Учетная запись базы данных Azure Cosmos DB restorable. |
| [ресторабледатабасеаккаунтслистресулт](#restorabledatabaseaccountslistresult) | Ответ операции List, содержащий учетные записи базы данных restorable и их свойства. |
| [рестораблелокатионресаурце](#restorablelocationresource) | Свойства учетной записи региона restorable. |

### <a name="apitype"></a><a id="apitype"></a>апитипе

Перечисление для указания типа API учетной записи базы данных restorable.

| **имя**; | **Тип** |
| --- | --- |
| Cassandra |строка|
| Gremlin |строка|
| GremlinV2 |строка|
| MongoDB |строка|
| SQL |строка|
| Таблица |строка|

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

### <a name="restorabledatabaseaccountgetresult"></a><a id="restorabledatabaseaccountgetresult"></a>ресторабледатабасеаккаунтжетресулт

Учетная запись базы данных Azure Cosmos DB restorable.

| **имя**; | **Тип** | **Описание** |
| --- | --- | --- |
| ID |строка| Уникальный идентификатор ресурса Azure Resource Manager ресурса. |
| location |строка| Расположение группы ресурсов, к которой принадлежит ресурс. |
| name |строка| Имя ресурса диспетчер ресурсов. |
| Properties. accountName |строка| Имя учетной записи глобальной базы данных. |
| Properties. Апитипе |[апитипе](#apitype)| Тип API учетной записи базы данных restorable. |
| Properties. creationTime |строка| Время создания учетной записи базы данных restorable (формат ISO-8601). |
| Properties. Делетионтиме |строка| Время удаления учетной записи базы данных restorable (формат ISO-8601). |
| Properties. Рестораблелокатионс |[Рестораблелокатионресаурце](#restorablelocationresource)[]| Список регионов, из которых можно восстановить учетную запись базы данных. |
| type |строка| Тип ресурса Azure. |

### <a name="restorabledatabaseaccountslistresult"></a><a id="restorabledatabaseaccountslistresult"></a>ресторабледатабасеаккаунтслистресулт

Ответ операции List, содержащий учетные записи базы данных restorable и их свойства.

| **имя**; | **Тип** | **Описание** |
| --- | --- | --- |
| значение |[Ресторабледатабасеаккаунтжетресулт](#restorabledatabaseaccountgetresult)[]| Список учетных записей базы данных restorable и их свойств. |

### <a name="restorablelocationresource"></a><a id="restorablelocationresource"></a>рестораблелокатионресаурце

Свойства учетной записи региона restorable.

| **имя**; | **Тип** | **Описание** |
| --- | --- | --- |
| creationTime |строка| Время создания учетной записи региональной базы данных restorable (формат ISO-8601). |
| делетионтиме |строка| Время удаления учетной записи региональной базы данных restorable (формат ISO-8601). |
| локатионнаме |строка| Расположение учетной записи региона restorable. |
| регионалдатабасеаккаунтинстанцеид |строка| Идентификатор экземпляра региональной учетной записи restorable. |

## <a name="next-steps"></a>Следующие шаги

* [Перечислите коллекции restorable](restorable-database-accounts-list-by-location.md) в API Azure Cosmos DB для MongoDB с помощью REST API.
* [Модель ресурсов](continuous-backup-restore-resource-model.md) режима непрерывного резервного копирования.