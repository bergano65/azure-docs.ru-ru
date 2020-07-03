---
title: Начать прямо | Azure Marketplace
description: API запуска инициирует динамический перечень процессов предложения.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: ef22f7720a4af2239c55d1a01f9d3f11c878d66e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81256320"
---
# <a name="go-live"></a>Запуск

> [!NOTE]
> Портал Cloud Partner API интегрированы с центром партнеров и продолжат работать после переноса ваших предложений в центр партнеров. Интеграция содержит небольшие изменения. Ознакомьтесь с изменениями, приведенными в [справочнике по портал Cloud Partner API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) , чтобы убедиться, что код будет продолжать работать после перехода в центр партнеров.

Этот API запускает процесс для принудительной отправки приложения рабочей среде. Выполнение этой операции занимает много времени. Этот вызов использует список уведомлений по электронной почте из [публикации](./cloud-partner-portal-api-publish-offer.md) операции API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
--------------

|  **Имя**      |   **Описание**                                                           | **Тип данных** |
|  --------      |   ---------------                                                           | ------------- |
| publisherid    | Идентификатор издателя для извлечения предложения, например `contoso`.       |  Строка       |
| offerId        | Идентификатор извлекаемой записи предложения.                                   |  Строка       |
| api-version    | Последняя версия API                                                   |  Дата         |
|  |  |  |

## <a name="header"></a>Заголовок
------

|  **Имя**       |     **Значение**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Авторизация   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>Пример текста запроса

### <a name="response"></a>Ответ

#### <a name="migrated-offers"></a>Перенесенные предложения

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Неперенесенные предложения

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Заголовок ответа

|  **Имя**             |      **Значение**                                                            |
|  --------             |      ----------                                                           |
| Расположение    |  Относительный путь для получения состояния этой операции            |
|  |  |

### <a name="response-status-codes"></a>Коды состояния ответа

| **Приведен** |  **Описание**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` — запрос был успешно принят. Ответ содержит расположение для отслеживания состояния операции. |
|  400     | `Bad/Malformed request` — дополнительные сведения об ошибке находятся в тексте ответа. |
|  404     |  `Not found` — указанная сущность не существует.                                       |
|  |  |
