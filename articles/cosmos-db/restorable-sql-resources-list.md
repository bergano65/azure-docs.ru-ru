---
title: Список ресурсов API SQL restorable в Azure Cosmos DB с помощью REST API
description: Возврат списка баз данных и полей со списком, существующих в учетной записи с заданной меткой времени и расположением. Это помогает в сценариях проверить, какие ресурсы существуют по заданной метке времени и расположению.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: e31423ae5afd4955ebe7acf1d456496f15f14a6b
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537295"
---
# <a name="list-restorable-sql-api-resources-in-azure-cosmos-db-using-rest-api"></a>Список ресурсов API SQL restorable в Azure Cosmos DB с помощью REST API

> [!IMPORTANT]
> Функция восстановления на момент времени (режим непрерывного резервного копирования) для Azure Cosmos DB в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Возврат списка баз данных и полей со списком, существующих в учетной записи с заданной меткой времени и расположением. Это помогает в сценариях проверить, какие ресурсы существуют по заданной метке времени и расположению. Для этого API требуется `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` разрешение.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlResources?api-version=2020-06-01-preview
```

С использованием необязательных параметров:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlResources?api-version=2020-06-01-preview&amp;restoreLocation={restoreLocation}&amp;restoreTimestampInUtc={restoreTimestampInUtc}
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
| 200 ОК | [рестораблесклресаурцеслистресулт](#restorablesqlresourceslistresult)| Операция выполнена успешно. |
| Другие коды состояния | [дефаултеррорреспонсе](#defaulterrorresponse)| Ошибочный ответ, описывающий причину сбоя операции. |

## <a name="examples"></a>Примеры

### <a name="cosmosdbrestorablesqlresourcelist"></a>космосдбрестораблесклресаурцелист

**Пример запроса**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlResources?api-version=2020-06-01-preview&amp;restoreLocation=WestUS&amp;restoreTimestampInUtc=10/13/2020 4:56
```

**Пример ответа**

Код состояния: 200.

```json
{
  "value": [
    {
      "databaseName": "Database1",
      "collectionNames": [
        "Container1"
      ]
    },
    {
      "databaseName": "Database2",
      "collectionNames": [
        "Container1",
        "Container2"
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

| Определение | Описание | | --- || --- | | [Датабасересторересаурце](#databaserestoreresource) | Определенные базы данных для восстановления. | | [Дефаултеррорреспонсе](#defaulterrorresponse) | Ошибочный ответ от службы. | | [ErrorResponse](#errorresponse) | Ошибочный ответ. | | [Рестораблесклресаурцеслистресулт](#restorablesqlresourceslistresult) | Ответ операции List, содержащий ресурсы SQL restorable. |

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

### <a name="restorablesqlresourceslistresult"></a><a id="restorablesqlresourceslistresult"></a>рестораблесклресаурцеслистресулт

Ответ операции List, содержащий ресурсы SQL restorable.

| **имя**; | **Тип** | **Описание** |
| --- | --- | --- |
| значение |[Датабасересторересаурце](#databaserestoreresource)[]| Список ресурсов restorable SQL, включая имена базы данных и коллекции. |

## <a name="next-steps"></a>Следующие шаги

* [Перечислите базы данных restorable](restorable-sql-databases-list.md) в API Azure Cosmos SQL с помощью REST API.
* [Модель ресурсов](continuous-backup-restore-resource-model.md) режима непрерывного резервного копирования.