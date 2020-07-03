---
title: Опубликовать предложение | Azure Marketplace
description: API для публикации указанного предложения.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 960d5facb53f20719045c5fdbe2179f549aca3f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255946"
---
# <a name="publish-an-offer"></a>Публикация предложения

> [!NOTE]
> Портал Cloud Partner API интегрированы с центром партнеров и продолжат работать после переноса ваших предложений в центр партнеров. Интеграция содержит небольшие изменения. Ознакомьтесь с изменениями, приведенными в [справочнике по портал Cloud Partner API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) , чтобы убедиться, что код будет продолжать работать после перехода в центр партнеров.

Запускает процесс публикации для указанного предложения. Этот вызов является длительной операцией.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
--------------

|  **Имя**      |    **Описание**                               |  **Тип данных** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherid   | Идентификатор издателя, например `contoso`      |   Строка       |
|  offerId       | Идентификатор предложения                                 |   Строка       |
|  api-version   | Последняя версия API                        |   Дата         |
|  |  |

## <a name="header"></a>Заголовок
------

|  **Имя**        |    **Значение**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Авторизация   |  `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Пример текста запроса
------------

### <a name="request"></a>Запрос

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>Свойства текста запроса

|  **Имя**               |   **Описание**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  электронные уведомления    | Список адресов электронной почты, разделенных запятыми, для получения уведомлений о ходе публикации. |
|  |  |


### <a name="response"></a>Ответ

#### <a name="migrated-offers"></a>Перенесенные предложения

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Неперенесенные предложения

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Заголовок ответа

|  **Имя**             |    **Значение**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Расположение    | Относительный путь для получения состояния этой операции     |
|  |  |


### <a name="response-status-codes"></a>Коды состояния ответа

| **Приведен** |  **Описание**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` — запрос был успешно принят. Ответ содержит местоположение, которое можно использовать для отслеживания запускаемой операции. |
| 400   | `Bad/Malformed request`. Текст ответа ошибки может предоставлять дополнительные сведения.                                                               |
| 422   | `Un-processable entity`. Указывает, что сущность, подлежащая публикации, не прошла проверку.                                                        |
| 404   | `Not found`. Объект, указанный клиентом, не существует.                                                                              |
|  |  |
