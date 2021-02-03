---
title: Список ресурсов restorable в API Azure Cosmos DB для MongoDB с помощью REST API
description: Возвращает список полей базы данных и коллекции, существующих в учетной записи с заданной меткой времени и расположением. Это помогает в сценариях проверить, какие ресурсы существуют по заданной метке времени и расположению.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: dc90bfb6325831276b3c6171b73aebfa2877cf68
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527922"
---
# <a name="list-restorable-resources-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Список ресурсов restorable в API Azure Cosmos DB для MongoDB с помощью REST API

Возвращает список полей базы данных и коллекции, существующих в учетной записи с заданной меткой времени и расположением. Это помогает в сценариях проверить, какие ресурсы существуют по заданной метке времени и расположению. Для этого API требуется `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` разрешение.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbResources?api-version=2020-06-01-preview
```

С использованием необязательных параметров:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbResources?api-version=2020-06-01-preview&amp;restoreLocation={restoreLocation}&amp;restoreTimestampInUtc={restoreTimestampInUtc}
```

## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Имя | В | Обязательно | Тип | Описание |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |строка| Идентификатор GUID instanceId учетной записи базы данных restorable. |
| **расположение** | path | True | строка| Azure Cosmos DB регион с пробелами между словами и каждым словом в прописной буквы. |
| **subscriptionId** | path | True | строка| Идентификатор целевой подписки. |
| **версия API** | query | True | строка | Версия API, используемая для этой операции. |
| **restoreLocation** | query | |строка| Расположение, в котором находятся ресурсы restorable. |
| **ресторетиместампинутк** | query | |строка| Метка времени существования ресурсов restorable. |

## <a name="responses"></a>Ответы

| Имя | Тип | Описание |
| --- | --- | --- |
| 200 ОК | [рестораблемонгодбресаурцеслистресулт](#restorablemongodbresourceslistresult)| Операция выполнена успешно. |
| Другие коды состояния | [дефаултеррорреспонсе](#defaulterrorresponse)| Ошибочный ответ, описывающий причину сбоя операции. |


## <a name="examples"></a>Примеры

### <a name="cosmosdbrestorablemongodbresourcelist"></a>космосдбрестораблемонгодбресаурцелист

**Пример запроса**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbResources?api-version=2020-06-01-preview&amp;restoreLocation=WestUS&amp;restoreTimestampInUtc=10/13/2020 4:56
```

**Пример ответа**

Код состояния: 200.

```json
{
  "value": [
    {
      "databaseName": "Database1",
      "collectionNames": [
        "Collection1"
      ]
    },
    {
      "databaseName": "Database2",
      "collectionNames": [
        "Collection1",
        "Collection2"
      ]
    },
    {
      "databaseName": "Database3",
      "collectionNames": []
    }
  ]
}
```

## <a name="definitions"></a>Определения

| Определение | Описание | | --- || --- | | [Датабасересторересаурце](#databaserestoreresource) | Определенные базы данных для восстановления. | | [Дефаултеррорреспонсе](#defaulterrorresponse) | Ошибочный ответ от службы. | | [ErrorResponse](#errorresponse) | Ошибочный ответ. | | [Рестораблемонгодбресаурцеслистресулт](#restorablemongodbresourceslistresult) | Ответ операции List, содержащий ресурсы SQL restorable. |

### <a name="databaserestoreresource"></a><a id="databaserestoreresource"></a>датабасересторересаурце

Определенные базы данных для восстановления.

| **имя**; | **Тип** | **Описание** |
| --- | --- | --- |
| коллектионнамес |string[]| Имена коллекций, доступных для восстановления. |
| databaseName |строка| Имя базы данных, доступной для восстановления. |

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

### <a name="restorablemongodbresourceslistresult"></a><a id="restorablemongodbresourceslistresult"></a>рестораблемонгодбресаурцеслистресулт

Ответ операции List, содержащий API restorable Azure Cosmos DB для ресурсов MongoDB.

| **имя**; | **Тип** | **Описание** |
| --- | --- | --- |
| значение |[Датабасересторересаурце](#databaserestoreresource)[]| Список restorable Azure Cosmos DB API для ресурсов MongoDB, включая имена базы данных и коллекции. |

## <a name="next-steps"></a>Следующие шаги

* [Перечислите базы данных restorable](restorable-mongodb-databases-list.md)  в API Azure Cosmos DB для MongoDB с помощью REST API.
* [Модель ресурсов](continuous-backup-restore-resource-model.md) режима непрерывного резервного копирования.