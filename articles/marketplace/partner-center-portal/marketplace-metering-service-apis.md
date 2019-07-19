---
title: API-интерфейсы службы измерения Marketplace | Azure Marketplace
description: Событие использования для предложений SaaS в Azure Marketplace.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 35e6c61a8e8537035d70323c85dfc7a76f87cbcd
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869564"
---
# <a name="marketplace-metering-service-apis"></a>API-интерфейсы службы из Marketplace для контроля потребления

API событий использования позволяет выдавать события использования для конкретной приобретенной сущности. Запрос на событие использования ссылается на измерение служб измерения, определенное издателем при публикации предложения.

## <a name="usage-event"></a>Событие использования

**POST**:`https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Параметры запроса:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | Версия операции, используемой для этого запроса. Последняя версия API — 2018-08-31. |

*Заголовки запроса:*

| Content-type       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Уникальное строковое значение для отслеживания запроса от клиента, желательно идентификатор GUID. Если это значение не указано, оно создается случайным образом и возвращается в заголовке ответа. |
| `x-ms-correlationid` | Уникальное строковое значение для операции на клиенте. Этот параметр сопоставляет все события от клиентской операции с событиями на стороне сервера. Если это значение не указано, оно будет создано и указано в заголовках ответа. |
| `authorization`   | [Получение токена носителя JSON Web Token (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Примечание. При выполнении HTTP-запроса префикс `Bearer` к маркеру, полученному по ссылке ссылки. |

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

### <a name="responses"></a>Responses

Код: 200<br>
ОК 

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
Неверный запрос, отсутствуют или указаны недопустимые данные или истек срок их действия

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
Неверный запрос, отсутствуют или указаны недопустимые данные или истек срок их действия

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

Код: 409<br>
Возникает конфликт, когда мы получаем вызов использования для идентификатора ресурса использования и эффективное использование, которое уже существует. Ответ будет содержать `additionalInfo` поле, содержащее сведения о принятом сообщении.

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

## <a name="batch-usage-event"></a>Событие использования пакетной службы

API событий использования пакетной службы позволяет создавать события использования для нескольких приобретенных сущностей одновременно. Запрос события использования пакета ссылается на измерение служб измерения, определенное издателем при публикации предложения.

>[!Note]
>Вы можете зарегистрировать несколько предложений SaaS на коммерческом рынке корпорации Майкрософт. Каждое зарегистрированное предложение SaaS имеет уникальное приложение Azure AD, зарегистрированное для проверки подлинности и авторизации. События, созданные в пакете, должны относиться к предложениям с тем же приложением Azure AD во время регистрации предложения.

**POST:** `https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Параметры запроса:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | Версия операции, используемой для этого запроса. Последняя версия API — 2018-08-31. |

*Заголовки запроса:*

| Content-type       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Уникальное строковое значение для отслеживания запроса от клиента, желательно идентификатор GUID. Если это значение не указано, оно будет создано и указано в заголовках ответа. |
| `x-ms-correlationid` | Уникальное строковое значение для операции на клиенте. Этот параметр сопоставляет все события от клиентской операции с событиями на стороне сервера. Если это значение не указано, оно будет создано и указано в заголовках ответа. |
| `authorization`      | [Получение токена носителя JSON Web Token (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Примечание. При выполнении HTTP-запроса префикс `Bearer` к маркеру, полученному по ссылке ссылки.  |

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
### <a name="responses"></a>Responses

Код: 200<br>
ОК

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

Описание кода состояния, упоминаемого `BatchUsageEvent` в ответе API:

| status code  | Описание |
| ---------- | -------------------- |
| `Accepted` | Принятый код. |
| `Expired` | Использование с истекшим сроком. |
| `Duplicate` | Указана повторяющаяся загрузка. |
| `Error` | Код ошибки. |
| `ResourceNotFound` | Указан недопустимый ресурс использования. |
| `ResourceNotAuthorized` | У вас нет разрешения на предоставление сведений об использовании для этого ресурса. |
| `InvalidDimension` | Измерение, для которого передано использование, недопустимо для этого предложения или плана. |
| `InvalidQuantity` | Переданное количество равно < 0. |
| `BadArgument` | Входные данные отсутствуют или имеют неправильный формат. |

Код: 400<br>
Неверный запрос, отсутствуют или указаны недопустимые данные или истек срок их действия

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
Пользователь не авторизован для выполнения этого вызова

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения см. в статье [лимитное выставление счетов SaaS](./saas-metered-billing.md).
