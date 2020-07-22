---
title: Переход на интерактивный API в Azure Marketplace
description: API запуска инициирует динамический перечень процессов предложения.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: dsindona
ms.author: dsindona
ms.date: 07/14/2020
ms.openlocfilehash: 5c5448a853447a0eacc8d974a5b00d5d1e8f4be2
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535949"
---
# <a name="go-live"></a>Запуск

> [!NOTE]
> Портал Cloud Partner API интегрированы с и будут продолжать работать в центре партнеров. Переход содержит небольшие изменения. Ознакомьтесь с изменениями, приведенными в [справочнике по портал Cloud Partner API](./cloud-partner-portal-api-overview.md) , чтобы убедиться, что код продолжит работать после перехода в центр партнеров. API-интерфейсы CPP следует использовать только для существующих продуктов, которые уже были интегрированы до перехода в центр партнеров. новые продукты должны использовать API-интерфейсы отправки центра партнеров.

Этот API запускает процесс для принудительной отправки приложения рабочей среде. Выполнение этой операции занимает много времени. Этот вызов использует список уведомлений по электронной почте из [публикации](./cloud-partner-portal-api-publish-offer.md) операции API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
--------------

|  **Имя**      |   **Описание**                                                           | **Data type** |
|  --------      |   ---------------                                                           | ------------- |
| publisherid    | Идентификатор издателя для извлечения предложения, например `contoso`.       |  Строка       |
| offerId        | Идентификатор извлекаемой записи предложения.                                   |  Строка       |
| api-version    | Последняя версия API                                                   |  Дата         |
|  |  |  |

## <a name="header"></a>Заголовок
------

|  **имя**;       |     **Значение**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Авторизация   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>Пример текста запроса

### <a name="response"></a>Ответ

#### <a name="migrated-offers"></a>Перенесенные предложения

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Предложения, которые не были перенесены

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Заголовок ответа

|  **имя**;             |      **Значение**                                                            |
|  --------             |      ----------                                                           |
| Расположение    |  Относительный путь для получения состояния этой операции            |
|  |  |

### <a name="response-status-codes"></a>Коды состояния ответа

| **Код** |  **Описание**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` — запрос был успешно принят. Ответ содержит расположение для отслеживания состояния операции. |
|  400     | `Bad/Malformed request` — дополнительные сведения об ошибке находятся в тексте ответа. |
|  404     |  `Not found` — указанная сущность не существует.                                       |
|  |  |
