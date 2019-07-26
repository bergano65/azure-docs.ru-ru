---
title: API выполнения SaaS версии 2 | Azure Marketplace
description: В этой статье объясняется, как создать предложение SaaS и управлять им в AppSource и Azure Marketplace с помощью связанных API-интерфейсов выполнения версии 2.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: a8196370a93a6ce8eed83002397c2f09efbc777f
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358585"
---
# <a name="saas-fulfillment-apis-version-2"></a>API-интерфейсы выполнения SaaS, версия 2 

В этой статье описываются интерфейсы API, которые позволяют партнерам продавать приложения SaaS в AppSource Marketplace и Azure Marketplace. Эти API-интерфейсы являются обязательным требованием для transactных предложений SaaS в AppSource и Azure Marketplace.

## <a name="managing-the-saas-subscription-life-cycle"></a>Управление жизненным циклом подписки SaaS

Azure SaaS управляет всем жизненным циклом покупки подписки SaaS. Он использует интерфейсы API выполнения в качестве механизма для управления фактом выполнения, изменения планов и удаления подписки с партнером. Счет клиента основан на состоянии подписки SaaS, которую поддерживает корпорация Майкрософт. На следующей диаграмме показаны состояния и операции, которые управляют изменениями состояния.

![Состояния жизненного цикла подписки SaaS](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Состояния подписки SaaS

В следующей таблице перечислены состояния подготовки для подписки SaaS, включая описание и схему последовательностей для каждой из них (если применимо). 

#### <a name="provisioning"></a>Идет подготовка

Когда клиент инициирует покупку, он получает эти сведения в коде авторизации на интерактивной веб-странице клиента, использующей параметр URL-адреса. Примером является `https://contoso.com/signup?token=..`, тогда как URL-адрес целевой страницы в центре партнеров — `https://contoso.com/signup`. Код авторизации можно проверить и переключить на сервер для получения сведений о службе подготовки, вызвав API разрешения.  Когда служба SaaS завершает подготовку, она отправляет вызов активации, чтобы сообщить о завершении выполнения и выплата за использование клиента. 

На следующей схеме показана последовательность вызовов API для сценария подготовки.  

![Вызовы API для подготовки службы SaaS](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Подготовлена

Это состояние является устойчивым состоянием подготовленной службы.

##### <a name="provisioning-for-update"></a>Подготовка к обновлению 

Это состояние означает, что обновление существующей службы находится в состоянии ожидания. Такое обновление может инициироваться клиентом либо из Marketplace, либо в службе SaaS (только для транзакций типа "прямой на клиента").

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>Подготовка к обновлению (при его запуске из Marketplace)

На следующей схеме показана последовательность действий при запуске обновления из Marketplace.

![Вызовы API при запуске обновления из Marketplace](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>Подготовка к обновлению (при его запуске из службы SaaS)

На следующей схеме показаны действия при запуске обновления из службы SaaS. (Вызов веб-перехватчика заменяется обновлением для подписки, инициированной службой SaaS.) 

![Вызовы API при запуске обновления из службы SaaS](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Приостановлен

Это состояние указывает, что платеж клиента не был получен. По политике мы предоставим клиенту льготный период, прежде чем отменять подписку. Когда подписка находится в этом состоянии: 

- Как партнер Вы можете уменьшить или заблокировать доступ пользователя к службе.
- Подписка должна храниться в восстанавливаемом состоянии, чтобы восстановить полную функциональность без потери данных или параметров. 
- В конце льготного периода необходимо получить запрос на восстановление для этой подписки через API-интерфейсы выполнения или запрос на отмену подготовки. 

#### <a name="unsubscribed"></a>Подписка отменена 

Подписки достигают этого состояния в ответ на явный запрос клиента или неуплату взносов. Ожидание от партнера заключается в том, что данные клиента сохранены для восстановления по запросу в течение определенного числа дней, а затем удаляются. 


## <a name="api-reference"></a>Справочник по API

В этом разделе документируется *API подписки* SaaS и *API операций*.  Значение `api-version` параметра для API-интерфейсов версии 2 — `2018-08-31`.  


### <a name="parameter-and-entity-definitions"></a>Определения параметров и сущностей

В следующей таблице перечислены определения общих параметров и сущностей, используемых API-интерфейсами выполнения.

|     Сущность или параметр     |     Определение                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | Идентификатор GUID для ресурса SaaS.  |
| `name`                   | Понятное имя, предоставленное клиентом для этого ресурса. |
| `publisherId`            | Уникальный строковый идентификатор для каждого издателя (например, Contoso). |
| `offerId`                | Уникальный строковый идентификатор для каждого предложения (например, "offer1").  |
| `planId`                 | Уникальный строковый идентификатор для каждого плана или номера SKU (например, "серебро"). |
| `operationId`            | Идентификатор GUID для определенной операции.  |
|  `action`                | Действие `unsubscribe`, выполняемое над ресурсом:, `suspend` `reinstate` `changePlan` ,или`changeQuantity`,. `transfer`  |
|   |   |

Глобальные уникальные идентификаторы ([GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)) — это 128-битные (32-шестнадцатеричные) номера, которые обычно создаются автоматически. 

#### <a name="resolve-a-subscription"></a>Разрешение подписки 

Конечная точка разрешения позволяет издателю разрешать токен Marketplace в постоянный идентификатор ресурса. Идентификатор ресурса — это уникальный идентификатор подписки SaaS. Когда пользователь перенаправляется на веб-сайт партнера, URL-адрес содержит маркер в параметрах запроса. Предполагается, что участник использует этот маркер и отдает запрос на его разрешение. Ответ содержит уникальный идентификатор подписки SaaS, имя, идентификатор предложения и план для ресурса. Этот маркер действителен только один час. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Опубликовать<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Версия операции, используемой для этого запроса.  |

*Заголовки запроса:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не указано, оно будет создано и указано в заголовках ответа. |
|  x-ms-correlationid |  Уникальное строковое значение для операции на стороне клиента. Этот параметр сопоставляет все события от клиентской операции с событиями на стороне сервера. Если это значение не указано, оно будет создано и указано в заголовках ответа.  |
|  авторизация     |  [Получение токена носителя JSON Web Token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Например: "`Bearer <access_token>`". |
|  x-ms-marketplace-token  |  Параметр запроса токена в URL-адресе, когда пользователь перенаправляется на веб-сайт партнера SaaS из Azure (например: `https://contoso.com/signup?token=..`). *Примечание.* URL-адрес декодирует значение маркера из браузера перед его использованием.  |

*Коды ответов:*

Код: 200<br>
Разрешает непрозрачный маркер в подписке SaaS. Текст ответа:
 

```json
{
    "id": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

Код: 400<br>
Недопустимый запрос. x-MS-Marketplace-Token отсутствует, неправильно сформирован или истек срок его действия.

Код: 403<br>
Не разрешено. Маркер проверки подлинности не указан или недопустим, либо запрос пытается получить доступ к приобретению, который не принадлежит текущему издателю.

Код: 404<br>
Не найдено.

Код: 500<br>
Внутренняя ошибка сервера.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>API подписки

API подписки поддерживает следующие операции HTTPS: **Получение**, **Публикация**, **исправление**и **Удаление**.


#### <a name="list-subscriptions"></a>Список подписок

Список всех подписок SaaS для издателя.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Получение<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Параметры запроса:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  Версия операции, используемой для этого запроса.  |

*Заголовки запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не указано, оно будет создано и указано в заголовках ответа. |
| x-ms-correlationid |  Уникальное строковое значение для операции на стороне клиента. Этот параметр сопоставляет все события от клиентской операции с событиями на стороне сервера. Если это значение не указано, оно будет создано и указано в заголовках ответа.  |
| авторизация      |  [Получение токена носителя JSON Web Token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Например: "`Bearer <access_token>`".  |

*Коды ответов:*

Код: 200 <br/>
Возвращает издателя и соответствующие подписки для всех предложений издателя на основе маркера проверки подлинности.
Полезные данные ответа:<br>

```json
{
  [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": "true", // true – the customer subscription is currently in free trial, false – the customer subscription is not currently in free trial.
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

Маркер продолжения будет присутствовать только в том случае, если есть дополнительные "страницы" для получения планов. 

Код: 403 <br>
Не разрешено. Маркер проверки подлинности не указан или недопустим, либо запрос пытается получить доступ к приобретению, который не принадлежит текущему издателю. 

Код: 500<br>
Внутренняя ошибка сервера.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>Получение подписки

Возвращает указанную подписку SaaS. Используйте этот вызов для получения сведений о лицензии и сведений о плане.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Получение<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Уникальный идентификатор подписки SaaS, полученной после разрешения маркера через API-интерфейс разрешения.   |
|  ApiVersion        |   Версия операции, используемой для этого запроса.   |

*Заголовки запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не указано, оно будет создано и указано в заголовках ответа. |
|  x-ms-correlationid |  Уникальное строковое значение для операции на стороне клиента. Этот параметр сопоставляет все события от клиентской операции с событиями на стороне сервера. Если это значение не указано, оно будет создано и указано в заголовках ответа.  |
|  авторизация     |  [Получение токена носителя JSON Web Token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Например: "`Bearer <access_token>`".  |

*Коды ответов:*

Код: 200<br>
Возвращает подписку SaaS по идентификатору. Полезные данные ответа:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"Contoso Cloud Solution",
        "publisherId": "contoso",
        "offerId": "offer1",
        "planId": "silver",
        "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "isFreeTrial": "true", // true – customer subscription is currently in free trial, false – customer subscription is not currently in free trial.
        "status": "Subscribed", // Indicates the status of the operation.
          "term": { //This gives the free trial term start and end date
            "startDate": "2019-05-31",
            "endDate": "2019-06-29",
            "termUnit": "P1M"
        },
}
```

Код: 403<br>
Не разрешено. Маркер проверки подлинности не указан или недопустим, либо запрос пытается получить доступ к приобретению, который не принадлежит текущему издателю.

Код: 404<br>
Не найдено.<br> 

Код: 500<br>
Внутренняя ошибка сервера.<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Список доступных планов

Используйте этот вызов, чтобы определить, существуют ли частные или открытые предложения для текущего издателя.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Получение<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   Версия операции, используемой для этого запроса.  |

*Заголовки запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не указано, оно будет создано и указано в заголовках ответа. |
|  x-ms-correlationid  | Уникальное строковое значение для операции на стороне клиента. Этот параметр сопоставляет все события от клиентской операции с событиями на стороне сервера. Если это значение не указано, оно будет создано и указано в заголовках ответа. |
|  авторизация     |  [Получение токена носителя JSON Web Token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Например: "`Bearer <access_token>`". |

*Коды ответов:*

Код: 200<br>
Возвращает список доступных планов для клиента. Текст ответа:

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

Код: 404<br>
Не найдено.<br> 

Код: 403<br>
Не разрешено. Маркер проверки подлинности не указан или недопустим, либо запрос пытается получить доступ к приобретению, который не принадлежит текущему издателю. <br> 

Код: 500<br>
Внутренняя ошибка сервера.<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>Активация подписки

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Опубликовать<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Версия операции, используемой для этого запроса.  |
| subscriptionId     | Уникальный идентификатор подписки SaaS, полученной после разрешения маркера с помощью API-интерфейса разрешения.  |

*Заголовки запроса:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не указано, оно будет создано и указано в заголовках ответа.  |
|  x-ms-correlationid  | Уникальное строковое значение для операции на стороне клиента. Эта строка сопоставляет все события от клиентской операции с событиями на стороне сервера. Если это значение не указано, оно будет создано и указано в заголовках ответа.  |
|  авторизация     |  [Получение токена носителя JSON Web Token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Например: "`Bearer <access_token>`". |

*Полезные данные запроса:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Коды ответов:*

Код: 200<br>
Активирует подписку.<br>

Код: 400<br>
Недопустимый запрос: ошибки проверки.

Код: 403<br>
Не разрешено. Маркер проверки подлинности не указан или недопустим, либо запрос пытается получить доступ к приобретению, который не принадлежит текущему издателю.

Код: 404<br>
Не найдено.

Код: 500<br>
Внутренняя ошибка сервера.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>Изменение плана в подписке

Обновите план в подписке.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Исправление<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Версия операции, используемой для этого запроса.  |
| subscriptionId     | Уникальный идентификатор подписки SaaS, полученной после разрешения маркера с помощью API-интерфейса разрешения.  |

*Заголовки запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не указано, оно будет создано и указано в заголовках ответа.  |
|  x-ms-correlationid  |  Уникальное строковое значение для операции на стороне клиента. Этот параметр сопоставляет все события от клиентской операции с событиями на стороне сервера. Если это значение не указано, оно будет создано и указано в заголовках ответа.    |
| авторизация      |  [Получение токена носителя JSON Web Token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Например: "`Bearer <access_token>`".  |

*Полезные данные запроса:*

```json
Request Body:
{
    "planId": "gold"
}
```

*Заголовки запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | Ссылка на ресурс для получения состояния операции.   |

*Коды ответов:*

Код: 202<br>
Запрос на изменение плана принят. Чтобы определить успешность или сбой, участник должен опросить операцию — расположение. <br>

Код: 400<br>
Недопустимый запрос: ошибки проверки.

Код: 403<br>
Не разрешено. Маркер проверки подлинности не указан или недопустим, либо запрос пытается получить доступ к приобретению, который не принадлежит текущему издателю.

Код: 404<br>
Не найдено.

Код: 500<br>
Внутренняя ошибка сервера.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Только план или количество могут быть исправлены одновременно, но не оба. Изменения в подписке с **обновлением** не `allowedCustomerOperations`включены.

#### <a name="change-the-quantity-on-the-subscription"></a>Изменение количества в подписке

Обновите количество в подписке.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Защиты<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Версия операции, используемой для этого запроса.  |
| subscriptionId     | Уникальный идентификатор подписки SaaS, полученной после разрешения маркера с помощью API-интерфейса разрешения.  |

*Заголовки запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не указано, оно будет создано и указано в заголовках ответа.  |
|  x-ms-correlationid  |  Уникальное строковое значение для операции на стороне клиента. Этот параметр сопоставляет все события от клиентской операции с событиями на стороне сервера. Если это значение не указано, оно будет создано и указано в заголовках ответа.    |
| авторизация      |  [Получение токена носителя JSON Web Token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Например: "`Bearer <access_token>`".  |

*Полезные данные запроса:*

```json
Request Body:
{
    "quantity": 5
}
```

*Заголовки запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | Ссылка на ресурс для получения состояния операции.   |

*Коды ответов:*

Код: 202<br>
Запрос на изменение количества принят. Чтобы определить успешность или сбой, участник должен опросить операцию — расположение. <br>

Код: 400<br>
Недопустимый запрос: ошибки проверки.


Код: 403<br>
Не разрешено. Маркер проверки подлинности не указан или недопустим, либо запрос пытается получить доступ к приобретению, который не принадлежит текущему издателю.

Код: 404<br>
Не найдено.

Код: 500<br>
Внутренняя ошибка сервера.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Только план или количество могут быть исправлены одновременно, но не оба. Изменения в подписке с **обновлением** не `allowedCustomerOperations`включены.

#### <a name="delete-a-subscription"></a>Удаление подписки

Отмена подписки и удаление указанной подписки.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionid-api-versionapiversion"></a>Оператор delete<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Версия операции, используемой для этого запроса.  |
| subscriptionId     | Уникальный идентификатор подписки SaaS, полученной после разрешения маркера с помощью API-интерфейса разрешения.  |

*Заголовки запроса:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не указано, оно будет создано и указано в заголовках ответа.   |
|  x-ms-correlationid  |  Уникальное строковое значение для операции на стороне клиента. Этот параметр сопоставляет все события от клиентской операции с событиями на стороне сервера. Если это значение не указано, оно будет создано и указано в заголовках ответа.   |
|  авторизация     |  [Получение токена носителя JSON Web Token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Например: "`Bearer <access_token>`".  |

*Коды ответов:*

Код: 202<br>
Партнер инициировал вызов, чтобы отменить подписку на подписку SaaS.<br>

Код: 400<br>
Удаление в подписке с **удалением** , `allowedCustomerOperations`не включенным в.

Код: 403<br>
Не разрешено. Маркер проверки подлинности не указан или недопустим, либо запрос пытается получить доступ к приобретению, который не принадлежит текущему издателю.

Код: 404<br>
Не найдено.

Код: 500<br>
Внутренняя ошибка сервера.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>API операций

API операций поддерживает следующие операции Patch и Get.

#### <a name="list-outstanding-operations"></a>Вывод списка невыполненных операций 

Выводит список необработанных операций для текущего издателя. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Получение<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Параметры запроса:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   Версия операции, используемой для этого запроса.                |
| subscriptionId     | Уникальный идентификатор подписки SaaS, полученной после разрешения маркера с помощью API-интерфейса разрешения.  |

*Заголовки запроса:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не указано, оно будет создано и указано в заголовках ответа.  |
|  x-ms-correlationid |  Уникальное строковое значение для операции на стороне клиента. Этот параметр сопоставляет все события от клиентской операции с событиями на стороне сервера. Если это значение не указано, оно будет создано и указано в заголовках ответа.  |
|  авторизация     |  [Получение токена носителя JSON Web Token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Например: "`Bearer <access_token>`".  |

*Коды ответов:*

Код: 200<br> Возвращает список ожидающих операций в подписке. Полезные данные ответа:

```json
[{
    "id": "<guid>",  
    "activityId": "<guid>",
    "subscriptionId": "<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```


Код: 400<br>
Недопустимый запрос: ошибки проверки.

Код: 403<br>
Не разрешено. Маркер проверки подлинности не указан или недопустим, либо запрос пытается получить доступ к приобретению, который не принадлежит текущему издателю.

Код: 404<br>
Не найдено.

Код: 500<br>
Внутренняя ошибка сервера.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>Получение состояния операции

Позволяет издателю контролировать состояние указанной асинхронной `subscribe`операции `changePlan`, активируемой триггером (например, `unsubscribe`, или `changeQuantity`).

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Получение<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Версия операции, используемой для этого запроса.  |

*Заголовки запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не указано, оно будет создано и указано в заголовках ответа.  |
|  x-ms-correlationid |  Уникальное строковое значение для операции на стороне клиента. Этот параметр сопоставляет все события от клиентской операции с событиями на стороне сервера. Если это значение не указано, оно будет создано и указано в заголовках ответа.  |
|  авторизация     |  [Получение токена носителя JSON Web Token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Например: "`Bearer <access_token>`".  |

*Коды ответов:*<br>

Код: 200<br> Возвращает указанную ожидающую операцию SaaS. Полезные данные ответа:

```json
Response body:
{
    "id  ": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}

```

Код: 400<br>
Недопустимый запрос: ошибки проверки.

Код: 403<br>
Не разрешено. Маркер проверки подлинности не указан или недопустим, либо запрос пытается получить доступ к приобретению, который не принадлежит текущему издателю.
 
Код: 404<br>
Не найдено.

Код: 500<br> Внутренняя ошибка сервера.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>Обновление состояния операции

Обновите состояние операции, чтобы указать на успех или неудачу в указанных значениях.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Исправление<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  Версия операции, используемой для этого запроса.  |
| subscriptionId     | Уникальный идентификатор подписки SaaS, полученной после разрешения маркера с помощью API-интерфейса разрешения.  |
|  operationId       | Операция, которая завершается. |

*Заголовки запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не указано, оно будет создано и указано в заголовках ответа. |
|  x-ms-correlationid |  Уникальное строковое значение для операции на стороне клиента. Этот параметр сопоставляет все события от клиентской операции с событиями на стороне сервера. Если это значение не указано, оно будет создано и указано в заголовках ответа. |
|  авторизация     |  [Получение токена носителя JSON Web Token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Например: "`Bearer <access_token>`".  |

*Полезные данные запроса:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Коды ответов:*

Код: 200<br> Вызов для информирования о завершении операции на стороне партнера. Например, этот ответ может сообщить об изменении рабочих мест или планов.

Код: 400<br>
Недопустимый запрос: ошибки проверки.

Код: 403<br>
Не разрешено. Маркер проверки подлинности не указан или недопустим, либо запрос пытается получить доступ к приобретению, который не принадлежит текущему издателю.

Код: 404<br>
Не найдено.

Код: 409<br>
Конфликта. Например, уже выполнено более новая транзакция.

Код: 500<br> Внутренняя ошибка сервера.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>Реализация веб-перехватчика в службе SaaS

Издатель должен реализовать веб-перехватчик в этой службе SaaS, чтобы заранее уведомлять пользователей об изменениях в своей службе. Служба SaaS должна вызывать API операций для проверки и авторизации перед выполнением действия в уведомлении веб-перехватчика.

```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher’s name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
В качестве действия может использоваться одно из следующих действий: 
- `unsubscribe`(когда ресурс был удален)
- `changePlan`(после завершения операции плана изменения)
- `changeQuantity`(по завершении операции изменения количества)
- `suspend`(когда ресурс был приостановлен)
- `reinstate`(когда ресурс был восстановлен после приостановки)

Состояние может быть одним из следующих: 
- **NotStarted** <br>
 - **InProgress** <br>
- **Успешно** <br>
- **Сбой** <br>
- **Конфликта** <br>

В уведомлении веб-перехватчика состояния, которые можно исправить, **успешно** и неудачны. Жизненный цикл операции — от **NotStarted** до состояния терминала, например " **успешно**", " **сбой**" или " **конфликт**". Если вы получаете **NotStarted** или в **ходе выполнения**, продолжайте запрашивать состояние с помощью Get API, пока операция не достигнет состояния терминала до выполнения действия. 

## <a name="mock-apis"></a>Макеты API

Наши интерфейсы API макетирования можно использовать для начала разработки, особенно для создания прототипов, а также для тестирования проектов. 

Конечная точка `https://marketplaceapi.microsoft.com/api` узла: (проверка подлинности не требуется)<br/>
Версия API:`2018-09-15`<br/>
Пример URI:`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

Пути к конечной точке API одинаковы для макетов и реальных интерфейсов API, но версии API различны. Версия предназначена `2018-09-15` для макетной версии и `2018-08-31` для рабочей версии. 

Любой из вызовов API в этой статье можно внести в конечную точку макета узла. Как правило, необходимо получать фиктивные данные в виде ответа. Вызовы методов подписки Update в макете API всегда возвращают 500. 

## <a name="next-steps"></a>Следующие шаги

Разработчики также могут программно получать и манипулировать рабочими нагрузками, предложениями и профилями издателей с помощью [портал Cloud Partner интерфейсов API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
