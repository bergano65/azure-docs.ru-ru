---
title: API службы контроля потребления (коммерческая платформа Майкрософт)
description: API событий потребления позволяет создавать события потребления для предложений SaaS в Microsoft AppSource и Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 95eba648219413923ce27d433a5236877c4953f3
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725471"
---
# <a name="marketplace-metering-service-apis"></a>API-интерфейсы службы из Marketplace для контроля потребления

API событий потребления позволяет создавать события потребления для отдельных приобретенных сущностей. Запрос события потребления ссылается на измерение служб контроля потребления, которое издатель определяет при публикации предложения.

## <a name="usage-event"></a>Событие потребления

**POST**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Параметры запроса:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | Версия операции, используемой для этого запроса. Последняя версия API: 2018-08-31. |

*Заголовки запроса:*

| Content-type       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не указано, оно создается случайным образом и возвращается в заголовке ответа. |
| `x-ms-correlationid` | Уникальное строковое значение для операции на стороне клиента. Этот параметр позволяет сопоставить все события клиентской операции с событиями на стороне сервера. Если это значение не указано, оно создается и возвращается в заголовках ответа. |
| `authorization`   | [Получение токена носителя JSON Web Token (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Примечание. При выполнении HTTP-запроса добавьте префикс `Bearer` к имени токена, полученному по ссылке. |

>[!Note]
>Для планов управляемых приложений Azure `resourceId` представляет собой `resourceUsageId`, размещенный в параметре `billingDetails` объекта метаданных для управляемого приложения.  Пример скрипта для его получения приведен в разделе [Использование токена удостоверений, управляемых Azure](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token).  Для предложений SaaS `resourceId` содержит идентификатор подписки SaaS.  Дополнительные сведения о подписках SaaS см. [в этом списке](./pc-saas-fulfillment-api-v2.md#list-subscriptions).

*Запрос:*

```json
{
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

### <a name="responses"></a>Ответы

Код: 200<br>
OK 

```json
{
  "usageEventId": "Unique identifier associated with the usage event",
  "status": "Accepted",
  "messageTime": "Time this message was created in UTC",
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

Код: 400 <br>
Неверный запрос, данные отсутствуют или недопустимы, истек срок действия данных

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

Код: 403<br>
Неверный запрос, данные отсутствуют или недопустимы, истек срок действия данных

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

Код: 409<br>
Конфликт, возникающий при получении вызова для идентификатора ресурса потребления и данных потребления, который уже существует. Ответ будет содержать поле `additionalInfo` с данными о принятом сообщении.

```json
{
  "code": "Conflict",
  "additionalInfo": {
    "usageEventId": "Unique identifier associated with the usage event",
    "status": "Accepted|NotProcessed|Expired",
    "messageTime": "Time this message was created in UTC",
    "resourceId": "Identifier of the resource against which usage is emitted",
    "quantity": 5.0,
    "dimension": "Dimension identifier",
    "effectiveStartTime": "Time in UTC when the usage event occurred",
    "planId": "Plan associated with the purchased offer"
  }
}
```

## <a name="batch-usage-event"></a>Пакетное событие потребления

API пакетных событий потребления позволяет одновременно создавать события потребления для нескольких приобретенных сущностей. Запрос пакетного события потребления ссылается на измерение служб контроля потребления, которое издатель определяет при публикации предложения.

>[!Note]
>Вы можете зарегистрировать несколько предложений SaaS на коммерческой платформе корпорации Майкрософт. Каждое зарегистрированное предложение SaaS содержит уникальное приложение Azure AD с регистрацией для целей аутентификации и авторизации. События, отправляемые в пакете, должны относиться к предложениям, которые содержали одинаковые приложения Azure AD на момент регистрации.

**POST:** `https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Параметры запроса:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | Версия операции, используемой для этого запроса. Последняя версия API: 2018-08-31. |

*Заголовки запроса:*

| Content-type       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не указано, оно создается случайным образом и возвращается в заголовках ответа. |
| `x-ms-correlationid` | Уникальное строковое значение для операции на стороне клиента. Этот параметр позволяет сопоставить все события клиентской операции с событиями на стороне сервера. Если это значение не указано, оно создается и возвращается в заголовках ответа. |
| `authorization`      | [Получение токена носителя JSON Web Token (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Примечание. При выполнении HTTP-запроса добавьте префикс `Bearer` к имени токена, полученному по ссылке.  |

*Запрос:*
```json
{
  "request": [
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    },
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    }
  ]
}
```
### <a name="responses"></a>Ответы

Код: 200<br>
OK

```json
{
  "count": 2,
  "result": [
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    },
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    }
  ]
}
```

Описание кода состояния, указанного в ответе API `BatchUsageEvent`:

| Код состояния  | Описание |
| ---------- | -------------------- |
| `Accepted` | Код принят. |
| `Expired` | Потребление с истекшим сроком действия. |
| `Duplicate` | Предоставлен дубликат сведений о потреблении. |
| `Error` | Код ошибки. |
| `ResourceNotFound` | Указан недопустимый ресурс потребления. |
| `ResourceNotAuthorized` | У вас нет разрешения на предоставление сведений о потреблении для этого ресурса. |
| `InvalidDimension` | Измерение, для которого предоставлены сведения о потреблении, недопустимо для этого предложения или плана. |
| `InvalidQuantity` | Переданное значение количества меньше 0. |
| `BadArgument` | Входное значение отсутствует или является недопустимым. |

Код: 400<br>
Неверный запрос, данные отсутствуют или недопустимы, истек срок действия данных

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "Invalid data format.",
      "target": "usageEventRequest",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```
Код: 403<br>
Пользователь не авторизован для этого вызова

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>Дальнейшие действия

Изучите дополнительные сведения о [выставлении счетов с контролем потребления SaaS](./saas-metered-billing.md).
