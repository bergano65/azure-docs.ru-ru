---
title: Перечисление баз данных restorable в API Azure Cosmos DB для MongoDB с помощью REST API
description: Отображение веб-канала событий всех изменений, выполненных во всех Azure Cosmos DB базах данных MongoDB в учетной записи restorable. Это помогает в ситуации, когда база данных была случайно удалена для получения времени удаления.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 64288f67728e59c32c662a6640d60daf52c53fe1
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527929"
---
# <a name="list-restorable-databases-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Перечисление баз данных restorable в API Azure Cosmos DB для MongoDB с помощью REST API

Отображение веб-канала событий всех изменений, выполненных во всех Azure Cosmos DB базах данных MongoDB в учетной записи restorable. Это помогает в ситуации, когда база данных была случайно удалена для получения времени удаления. Для этого API требуется `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` разрешение

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbDatabases?api-version=2020-06-01-preview
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
| 200 ОК | [рестораблемонгодбдатабасеслистресулт](#restorablemongodbdatabaseslistresult)| Операция выполнена успешно. |
| Другие коды состояния | [дефаултеррорреспонсе](#defaulterrorresponse)| Ошибочный ответ, описывающий причину сбоя операции.|

## <a name="examples"></a>Примеры

### <a name="cosmosdbrestorablemongodbdatabaselist"></a>космосдбрестораблемонгодбдатабаселист

**Пример запроса**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases?api-version=2020-06-01-preview
```

**Пример ответа**

Код состояния: 200.

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableMongodbDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Create"
        }
      }
    },
    {
      "id": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Delete"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Определения

| Определение | Описание | | --- || --- | | [Дефаултеррорреспонсе](#defaulterrorresponse) | Ошибочный ответ от службы. | | [ErrorResponse](#errorresponse) | Ошибочный ответ. | | [OperationType](#operationtype) | Перечисление для указания типа операции события. | | [Ресурс](#resource) | Ресурс Azure Cosmos DB API для события базы данных MongoDB | | [Рестораблемонгодбдатабасежетресулт](#restorablemongodbdatabasegetresult) | Azure Cosmos DB API для события базы данных MongoDB | | [Рестораблемонгодбдатабасепропертиес](#restorablemongodbdatabaseproperties) | Свойства Azure Cosmos DB API для события базы данных MongoDB | | [Рестораблемонгодбдатабасеслистресулт](#restorablemongodbdatabaseslistresult) | Ответ операции List, содержащий Azure Cosmos DB API для событий базы данных MongoDB и их свойств. |

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

### <a name="resource"></a><a id="resource"></a>Ресурсов

Ресурс Azure Cosmos DB API для события базы данных MongoDB

| **имя**; | **Тип** | **Описание** |
| --- | --- | --- |
| _rid |строка| Свойство, созданное системой. Уникальный идентификатор. |
| eventTimestamp |строка| Время, когда произошло данное событие базы данных. |
| "тип_операции" |[OperationType](#operationtype)| Тип операции этого события базы данных.  |
| ownerId |строка| Имя Azure Cosmos DB API для базы данных MongoDB.|
| овнерресаурцеид |строка| Идентификатор ресурса Azure Cosmos DB API для базы данных MongoDB. |

### <a name="restorablemongodbdatabasegetresult"></a><a id="restorablemongodbdatabasegetresult"></a>рестораблемонгодбдатабасежетресулт

Azure Cosmos DB API для события базы данных MongoDB

| **имя**; | **Тип** | **Описание** |
| --- | --- | --- |
| ID |строка| Уникальный идентификатор ресурса Azure Resource Manager ресурса. |
| name |строка| Имя ресурса диспетчер ресурсов. |
| properties |[рестораблемонгодбдатабасепропертиес](#restorablemongodbdatabaseproperties)| Свойства Azure Cosmos DB API для события базы данных MongoDB. |
| type |строка| Тип ресурса Azure. |

### <a name="restorablemongodbdatabaseproperties"></a><a id="restorablemongodbdatabaseproperties"></a>рестораблемонгодбдатабасепропертиес

Свойства Azure Cosmos DB API для события базы данных MongoDB

| **имя**; | **Тип** | **Описание** |
| --- | --- | --- |
| resource |[Ресурс](#resource)| Ресурс Azure Cosmos DB API для события базы данных MongoDB |

### <a name="restorablemongodbdatabaseslistresult"></a><a id="restorablemongodbdatabaseslistresult"></a>рестораблемонгодбдатабасеслистресулт

Ответ операции списка, который содержит события базы данных и их свойства.

| **имя**; | **Тип** | **Описание** |
| --- | --- | --- |
| значение |[Рестораблемонгодбдатабасежетресулт](#restorablemongodbdatabasegetresult)[]| Список событий базы данных и их свойства. |

## <a name="next-steps"></a>Следующие шаги

* [Перечислите ресурсы restorable](restorable-mongodb-resources-list.md)  в API Azure Cosmos DB для MongoDB с помощью REST API.
* [Перечислите коллекции restorable](restorable-mongodb-collections-list.md)  в API Azure Cosmos DB для MongoDB с помощью REST API.
* [Модель ресурсов](continuous-backup-restore-resource-model.md) режима непрерывного резервного копирования.