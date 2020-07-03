---
title: API операции отмены | Azure Marketplace
description: Отмена операций.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: f9e55ff2c581f9392a125f6dc3ec8d903e9876a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81256439"
---
# <a name="cancel-operation"></a>Отмена операции

> [!NOTE]
> Портал Cloud Partner API интегрированы с центром партнеров и продолжат работать после переноса ваших предложений в центр партнеров. Интеграция содержит небольшие изменения. Ознакомьтесь с изменениями, приведенными в [справочнике по портал Cloud Partner API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) , чтобы убедиться, что код будет продолжать работать после перехода в центр партнеров.

Этот API в настоящее время отменяет операцию на предложение. Используйте [получения операций API](./cloud-partner-portal-api-retrieve-operations.md) для получения `operationId`, чтобы передать его в API. Отмена обычно выполняется в синхронной операции, однако в некоторых сложных сценариях новой операции может потребоваться отменить существующую. В этом случае в тексте ответа HTTP содержит расположение операции, который необходим для запроса состояния.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

--------------

|  **Имя**    |      **Описание**                                  |    **Тип данных**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherid  |  Идентификатор издателя, например `contoso`         |   Строка          |
| offerId      |  Идентификатор предложения                                     |   Строка          |
| api-version  |  Текущая версия клиента API                               |    Дата           |
|  |  |  |

## <a name="header"></a>Заголовок
------

|  **Имя**              |  **Значение**         |
|  ---------             |  ----------        |
|  Content-Type          |  приложение/json  |
|  Авторизация         |  Маркер носителя YOUR TOKEN |
|  |  |

## <a name="body-example"></a>Пример текста запроса
------------

### <a name="request"></a>Запрос

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Свойства текста запроса

|  **Имя**                |  **Описание**                                               |
|  --------                |  ---------------                                               |
|  электронные уведомления     | Список адресов электронной почты, разделенных запятыми, для получения уведомлений о ходе публикации. |
|  |  |

### <a name="response"></a>Ответ

#### <a name="migrated-offers"></a>Перенесенные предложения

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Неперенесенные предложения

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Заголовок ответа

|  **Имя**             |    **Значение**                       |
|  ---------            |    ----------                      |
| Расположение    | Относительный путь для получения состояния этой операции. |
|  |  |

### <a name="response-status-codes"></a>Коды состояния ответа

| **Приведен**  |  **Описание**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | Все в порядке. Запрос успешно обработан и выполнение операции синхронно отменяется. |
|  202      | Принято. Запрос успешно обработан и происходит отмена операции. Расположение операции отмены возвращается в заголовок ответа. |
|  400      | Неправильный/недопустимый запрос. Текст ответа ошибки может предоставлять дополнительные сведения.  |
|  403      | Доступ запрещен. Клиент имеет доступ к указанному в запросе пространству имен. |
|  404      | Не найдено. Указанная сущность не существует. |
|  |  |
