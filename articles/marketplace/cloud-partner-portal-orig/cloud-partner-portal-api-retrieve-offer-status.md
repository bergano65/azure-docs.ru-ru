---
title: Получить состояние предложения | Azure Marketplace
description: API получает текущее состояние предложения.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 9cf6ca27101a08ff58f32dcd31413256762490a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255925"
---
# <a name="retrieve-offer-status"></a>Получение состояния предложения

> [!NOTE]
> Портал Cloud Partner API интегрированы с центром партнеров и продолжат работать после переноса ваших предложений в центр партнеров. Интеграция содержит небольшие изменения. Ознакомьтесь с изменениями, приведенными в [справочнике по портал Cloud Partner API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) , чтобы убедиться, что код будет продолжать работать после перехода в центр партнеров.

Получение текущего состояния предложения.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

|  **Имя**       |   **Описание**                            |  **Тип данных** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherid    | Идентификатор издателя, например `Contoso`  |     Строка     |
|  offerId        | Глобальный уникальный идентификатор уникально идентифицирующий предложение      |     Строка     |
|  api-version    | API последней версии                        |     Дата       |
|  |  |


## <a name="header"></a>Заголовок


|  Имя           |  Значение               |
|  -------------  | -------------------  |
|  Content-Type   |  `application/json`  |
|  Авторизация  | `Bearer YOUR_TOKEN`  |
|  |  |

## <a name="body-example"></a>Пример текста запроса


### <a name="response"></a>Ответ

``` json
  {
      "status": "succeeded",
      "messages": [],
      "steps": [
      {
          "estimatedTimeFrame": "< 15 min",
          "id": "displaydummycertify",
          "stepName": "Validate Pre-Requisites",
          "description": "Offer settings provided are validated.",
          "status": "complete",
          "messages": [
              {
                  "messageHtml": "Step completed.",
                  "level": "information",
                  "timestamp": "2018-03-16T17:50:45.7215661Z"
              }
          ],       
          "progressPercentage": 100
      },
      {
          "estimatedTimeFrame": "~2-3 days",
          "id": "displaycertify",
          "stepName": "Certification",
          "description": "Your offer is analyzed by our certification systems for issues.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 day",
          "id": "displayprovision",
          "stepName": "Provisioning",
          "description": "Your virtual machine is being replicated in our production systems.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "displaypackage",
          "stepName": "Packaging and Lead Generation Registration",
          "description": "Your virtual machine is being packaged for customers. Additionally, lead systems are being configured and set up.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "publisher-signoff",
          "stepName": "Publisher signoff",
          "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "~2-5 days",
          "id": "live",
          "stepName": "Live",
          "description": "Offer is publicly visible and is available for purchase.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      }
      ],
      "previewLinks": [],
      liveLinks": [],
  }
```


### <a name="response-body-properties"></a>Свойства текста ответа

|  **Имя**             |    **Описание**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | Состояние предложения. Список возможных значений, см. ниже в разделе [Состояние предложения](#offer-status). |
|  отправляемых из облака на устройство             | Массив сообщений связанный с предложением                                                    |
|  steps                | Массив шагов, которые предлагает предложение во время публикации                      |
|  estimatedTimeFrame   | Оценка времени, необходимого для завершения этого шага в удобном формате                       |
|  идентификатор                   | Идентификатор шага                                                                         |
|  stepName             | Имя шага                                                                               |
|  description          | Описание шага                                                                        |
|  status               | Состояние шага. Список возможных значений, см. ниже в разделе [Состояние шага](#step-status).    |
|  отправляемых из облака на устройство             | Массив сообщений, относящихся к шагу                                                          |
|  processPercentage    | Процент выполнения шага                                                              |
|  previewLinks         | *В настоящее время не реализован*                                                                    |
|  liveLinks            | *В настоящее время не реализован*                                                                    |
|  notificationEmails   | Не рекомендуется для предложений, перенесенных в центр партнеров. Уведомления электронной почты для перенесенных предложений будут отправляться по электронной почте, указанной в поле Контактная информация продавца в параметрах учетной записи.<br><br>Для неперенесенных предложений список адресов электронной почты с разделителями-запятыми для уведомления о ходе выполнения операции        |
|  |  |

### <a name="response-status-codes"></a>Коды состояния ответа

| **Приведен** |   **Описание**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK`. Запрос успешно обработан и текущее состояние предложения возвращено. |
|  400     | `Bad/Malformed request` — текст ответа ошибки может содержать дополнительные сведения.                 |
|  404     | `Not found` — указанная сущность не существует.                                                |
|  |  |

### <a name="offer-status"></a>Состояние предложения

|  **Имя**                    |    **Описание**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | Предложение не было опубликовано.                          |
|  NotStarted                  | Предложение новое и не запущенное.                            |
|  WaitingForPublisherReview   | Предложение ожидает утверждения издателя.                 |
|  Запущен                     | Отправка предложения обрабатывается.                     |
|  Успешно                   | Отправка предложения прекратила обработку.               |
|  Отменено                    | Отправка предложения была отменена.                           |
|  Failed                      | Не удалось отправить предложение.                                 |
|  |  |

### <a name="step-status"></a>Состояние шага

|  **Имя**                    |    **Описание**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | Выполнение шага не началось.                        |
|  InProgress                  | Шаг выполняется.                             |
|  WaitingForPublisherReview   | Шаг ожидает утверждения издателя.      |
|  WaitingForApproval          | Шаг ожидает утверждения процесса.        |
|  Заблокировано                     | Шаг заблокирован.                             |
|  Отклонено                    | Шаг отклонен.                            |
|  Завершение                    | Шаг завершен.                            |
|  Отменено                    | Шаг отменен.                           |
|  |  |
