---
title: АПЫ учета рынка (ru) Лазурный рынок
description: Событие использования предложений SaaS в Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 315f36e5aed9dee0a89e1f9f504b18a6bed806e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275753"
---
# <a name="marketplace-metering-service-apis"></a>API-интерфейсы службы из Marketplace для контроля потребления

API события использования позволяет излучать события использования для определенной приобретенной сущности. Запрос на событие использования ссылается на измерение услуг учета, определяемое издателем при публикации предложения.

## <a name="usage-event"></a>Событие использования

**СООБЩЕНИЕ**:`https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Параметры запроса:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | Версия операции, используемой для этого запроса. Последняя версия API 2018-08-31. |

*Запрос заголовки:*

| Content-type       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Уникальное значение строки для отслеживания запроса от клиента, предпочтительно GUID. Если это значение не указано, оно создается случайным образом и возвращается в заголовке ответа. |
| `x-ms-correlationid` | Уникальное значение строки для работы на клиенте. Этот параметр коррелирует все события от работы клиента с событиями на стороне сервера. Если это значение не предусмотрено, он будет сгенерирован и представлен в заголовках ответов. |
| `authorization`   | [Получите токен для веб-токов JSON (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Примечание: При составлении запроса HTTP припреживать `Bearer` к маркеру, полученному из ссылки. |

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
Плохой запрос, отсутствующие или недействительные данные предоставлены или истек

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
Плохой запрос, отсутствующие или недействительные данные предоставлены или истек

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

Код: 409<br>
Конфликт, когда мы получаем вызов использования для идентификатора ресурса использования, и эффективное использование, которое уже существует. Ответ будет `additionalInfo` содержать поле, содержащее информацию о принятом сообщении.

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

## <a name="batch-usage-event"></a>Событие использования пакета

API использования пакетов позволяет излучать события использования для нескольких приобретенных объектов одновременно. Запрос события пакетного использования ссылается на измерение услуг учета, определенное издателем при публикации предложения.

>[!Note]
>Вы можете зарегистрировать несколько предложений SaaS на коммерческом рынке Корпорации Майкрософт. Каждое зарегистрированное предложение SaaS имеет уникальное приложение Azure AD, которое зарегистрировано для целей проверки подлинности и авторизации. События, испускаемые в пакете, должны принадлежать предложениям с тем же приложением Azure AD на момент регистрации предложения.

**ПОЧТА:**`https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Параметры запроса:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | Версия операции, используемой для этого запроса. Последняя версия API 2018-08-31. |

*Запрос заголовки:*

| Content-type       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Уникальное значение строки для отслеживания запроса от клиента, предпочтительно GUID. Если это значение не предусмотрено, одно будет сгенерировано и представлено в заголовках ответов. |
| `x-ms-correlationid` | Уникальное значение строки для работы на клиенте. Этот параметр коррелирует все события от работы клиента с событиями на стороне сервера. Если это значение не предусмотрено, одно будет сгенерировано и предоставлено в заголовках ответов. |
| `authorization`      | [Получите токен для веб-токов JSON (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Примечание: При составлении запроса HTTP припреживать `Bearer` к маркеру, полученному из ссылки.  |

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

Описание кода статуса, `BatchUsageEvent` на который ссылается aPI:

| Код состояния  | Описание |
| ---------- | -------------------- |
| `Accepted` | Принятый код. |
| `Expired` | Использование с истекшим сроком годности. |
| `Duplicate` | Двойное использование при условии. |
| `Error` | Код ошибки. |
| `ResourceNotFound` | Предоставляемый ресурс использования является недействительным. |
| `ResourceNotAuthorized` | Вы не уполномочены предоставлять использование этого ресурса. |
| `InvalidDimension` | Измерение, для которого используется передается недействительным для этого предложения / плана. |
| `InvalidQuantity` | Количество пройденных составляет < 0. |
| `BadArgument` | Вход отсутствует или пороки. |

Код: 400<br>
Плохой запрос, отсутствующие или недействительные данные предоставлены или истек

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
Пользователь не имеет к этому вызова

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации, см [SaaS дозированных биллинга](./saas-metered-billing.md).
