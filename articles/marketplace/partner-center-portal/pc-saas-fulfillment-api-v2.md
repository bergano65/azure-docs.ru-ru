---
title: SaaS Выполнение API v2 Лазурный рынок
description: В этой статье объясняется, как создавать и управлять предложением SaaS на AppSource и Azure Marketplace с помощью связанных с ними AAP v2.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/18/2019
ms.author: dsindona
ms.openlocfilehash: ca49418013357ecaae62ea5e91374eaa1cbde59d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275770"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS fulfillment APIs, version 2 (API-интерфейсы исполнения SaaS, версия 2) 

В этой статье подробно описаны AI, которые позволяют партнерам продавать свои приложения SaaS на рынке AppSource и на рынке Azure Marketplace. Эти AA являются требованием для трансформативных предложений SaaS на AppSource и Azure Marketplace.

## <a name="managing-the-saas-subscription-life-cycle"></a>Управление жизненным циклом подписки SaaS

Azure SaaS управляет всем жизненным циклом покупки подписки SaaS. Он использует AIS выполнения как механизм для привода фактического выполнения, изменения в планах, и удаление подписки с партнером. Счет клиента основан на состоянии подписки SaaS, которую поддерживает корпорация Майкрософт. Следующая диаграмма изображает состояния и операции, которые управляют изменениями между состояниями.

![SaaS подписки жизненного цикла состояния](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Государства подписки SaaS

В следующей таблице перечислены состояния подготовки к подписке SaaS, включая описание и диаграмму последовательности для каждого из них (если это применимо). 

#### <a name="provisioning"></a>Подготовка

Когда клиент инициирует покупку, партнер получает эту информацию в коде авторизации на интерактивной веб-странице, использующей параметр URL. Примером `https://contoso.com/signup?token=..`является, в то время как `https://contoso.com/signup`URL-адрес целевой страницы в Центре Партнеров . Код авторизации может быть проверен и обменен на подробную информацию об услуге предоставления, позвонив в API Resolve.  Когда служба SaaS завершает подготовку, она отправляет активационный звонок, чтобы сигнализировать, что выполнение завершено и клиент может быть выставлен счет. 

На следующей диаграмме показана последовательность API-вызова для сценария подготовки.  

![API призывает к предоставлению услуги SaaS](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Подготовлено

Это состояние является устойчивым состоянием приготовительной услуги.

##### <a name="provisioning-for-update"></a>Подготовка к обновлению 

Это состояние означает, что обновление существующей службы находится на рассмотрении. Такое обновление может быть инициировано клиентом, либо с рынка, либо на сервисе SaaS (только для прямых транзакций с клиентом).

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>Подготовка к обновлению (когда оно инициировано с рынка)

На следующей диаграмме отображается последовательность действий, когда обновление инициируется с рынка.

![Вызовы API при инициировании обновления с рынка](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>Подготовка к обновлению (когда оно инициировано службой SaaS)

На следующей диаграмме показаны действия, когда обновление инициируется из службы SaaS. (Вызов webhook заменяется обновлением подписки, инициированной службой SaaS.) 

![Вызовы API при инициировании обновления из службы SaaS](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Приостановлена

Это состояние указывает на то, что платеж клиента не был получен. По политике мы предоставим клиенту льготный период перед отменой подписки. Когда подписка находится в таком состоянии: 

- Как партнер, вы можете ухудшить или заблокировать доступ пользователя к службе.
- Подписка должна быть сохранена в восстанавливаемом состоянии, которое может восстановить полную функциональность без потери данных или настроек. 
- Ожидайте получить запрос на восстановление этой подписки через AIS выполнения или запрос на де-предоставление в конце льготного периода. 

#### <a name="unsubscribed"></a>Отписаны 

Подписки достигают этого состояния в ответ либо на явный запрос клиента, либо на неуплату платежей. Ожидается, что данные клиента сохраняются для восстановления по запросу в течение определенного количества дней, а затем удаляются. 


## <a name="api-reference"></a>Справочник по интерфейсам API

В этом разделе документируется *API* Подписки SaaS и *API операций.*  Значение параметра `api-version` для AA версии 2 . `2018-08-31`  


### <a name="parameter-and-entity-definitions"></a>Определения параметров и сущности

В следующей таблице перечислены определения общих параметров и сущностей, используемых AIS выполнения.

|     Сущность/Параметр     |     Определение                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | Идентификатор GUID для ресурса SaaS.  |
| `name`                   | Дружественное название, предусмотренное для этого ресурса клиентом. |
| `publisherId`            | Уникальный идентификатор строки для каждого издателя (например, "contoso"). |
| `offerId`                | Уникальный идентификатор строки для каждого предложения (например: "offer1").  |
| `planId`                 | Уникальный идентификатор строки для каждого плана/SKU (например: "серебро"). |
| `operationId`            | Идентификатор GUID для конкретной операции.  |
|  `action`                | Действие выполняется на ресурсе, `Unsubscribe`либо `ChangePlan`, `ChangeQuantity` `Suspend` `Transfer`, `Reinstate`или , . |
|   |   |

Глобально уникальные идентификаторы[(GUIDs](https://en.wikipedia.org/wiki/Universally_unique_identifier)) являются 128-битные (32-гексадецимальные) номера, которые обычно автоматически генерируются. 

#### <a name="resolve-a-subscription"></a>Разрешить подписку 

Конечная точка разрешения позволяет издателю решить маркер рынка с постоянным идентификатором ресурсов. Идентификатор ресурса является уникальным идентификатором для подписки SaaS. Когда пользователь перенаправляется на веб-сайт партнера, URL содержит маркер в параметрах запроса. Ожидается, что партнер воспользуется этим токеном и сделает запрос на его решение. Ответ содержит уникальный идентификатор подписки SaaS, имя, идентификатор предложения и план ресурса. Этот маркер действителен только в течение одного часа. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Post<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Версия операции, используемой для этого запроса.  |

*Запрос заголовки:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не предусмотрено, он будет сгенерирован и представлен в заголовках ответов. |
|  x-ms-correlationid |  Уникальное строковое значение для операции на стороне клиента. Этот параметр коррелирует все события от работы клиента с событиями на стороне сервера. Если это значение не предусмотрено, он будет сгенерирован и представлен в заголовках ответов.  |
|  авторизация     |  [Получить токен для веб-токов JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Например:`Bearer <access_token>`". |
|  x-ms-marketplace-token  |  Параметр запроса токенов в URL-адресе при перенаправленном пользователем на веб-сайт `https://contoso.com/signup?token=..`партнера SaaS из Azure (например: ). *Примечание:* URL-адрес декодирует значение маркера из браузера перед его использованием.  |

*Коды ответов:*

Код: 200<br>
Разрешает непрозрачный токен с подпиской SaaS. Орган реагирования:
 

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
Недопустимый запрос. x-ms-marketplace-токен отсутствует, недоформируется или просрочен.

Код: 403<br>
Не авторизовано. Токен проверки подлинности не был предоставлен или является недействительным, или запрос пытается получить доступ к приобретению, которое не принадлежит текущему издателю.

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

API подписки поддерживает следующие операции HTTPS: **Получить,** **Опубликовать,** **Патч,** и **Удалить**.


#### <a name="list-subscriptions"></a>Список подписок

Перечисляет все подписки SaaS для издателя.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Получить<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Параметры запроса:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  Версия операции, используемой для этого запроса.  |

*Запрос заголовки:*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не предусмотрено, он будет сгенерирован и представлен в заголовках ответов. |
| x-ms-correlationid |  Уникальное строковое значение для операции на стороне клиента. Этот параметр коррелирует все события от работы клиента с событиями на стороне сервера. Если это значение не предусмотрено, он будет сгенерирован и представлен в заголовках ответов.  |
| авторизация      |  [Получить токен для веб-токов JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Например:`Bearer <access_token>`".  |

*Коды ответов:*

Код: 200 <br/>
Получает издателя и соответствующие подписки на все предложения издателя, основываясь на маркере аутентификации.

>[!Note]
>[Mock AIS](#mock-apis) используются при первом разработке предложения, в то время как реальные AA должны быть использованы при фактической публикации предложения.  Реальные AБИ и Мак-АДИ отличаются первой строкой кода.  В реальном API есть `subscription` раздел, в то время как этот раздел не существует для макета API.

Полезная нагрузка отклика для макета API:<br>

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
          "isFreeTrial": "true", // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```
И для реального API: <br>

```json
{
  "subscriptions": [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant, object id and email address for which SaaS subscription is purchased.
              "emailId": "<email>",
              "objectId": "<guid>",                     
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant, object id and email address that purchased the SaaS subscription. These could be different for reseller scenario
              "emailId": "<email>",
              "objectId": "<guid>",                      
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
          "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.(optional field - default false)
          "isTest": false, //indicating whether the current subscription is a test asset
          "sandboxType": "None", // Possible Values: None, Csp (Csp sandbox purchase)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "@nextLink": ""
}
```
Продолжение токена будет присутствовать только при наличии дополнительных "страниц" планов для извлечения. 

Код: 403 <br>
Не авторизовано. Токен проверки подлинности не был предоставлен или является недействительным, или запрос пытается получить доступ к приобретению, которое не принадлежит текущему издателю. 

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

#### <a name="get-subscription"></a>Получить подписку

Получает указанную подписку SaaS. Используйте этот вызов, чтобы получить информацию о лицензии и спланировать информацию.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Получить<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Уникальный идентификатор подписки SaaS, полученный после разрешения токена через API Resolve.   |
|  ApiVersion        |   Версия операции, используемой для этого запроса.   |

*Запрос заголовки:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не предусмотрено, он будет сгенерирован и представлен в заголовках ответов. |
|  x-ms-correlationid |  Уникальное строковое значение для операции на стороне клиента. Этот параметр коррелирует все события от работы клиента с событиями на стороне сервера. Если это значение не предусмотрено, он будет сгенерирован и представлен в заголовках ответов.  |
|  авторизация     |  [Получить токен для веб-токов JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Например:`Bearer <access_token>`".  |

*Коды ответов:*

Код: 200<br>
Получает подписку SaaS из идентификатора. Полезная нагрузка ответа:<br>

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
        "isFreeTrial": "true", // true - customer subscription is currently in free trial, false - customer subscription is not currently in free trial.
        "status": "Subscribed", // Indicates the status of the operation.
          "term": { //This gives the free trial term start and end date
            "startDate": "2019-05-31",
            "endDate": "2019-06-29",
            "termUnit": "P1M" //where P1M: Monthly, P1Y: Yearly 
        },
}
```

Код: 403<br>
Не авторизовано. Токен проверки подлинности не был предоставлен или является недействительным, или запрос пытается получить доступ к приобретению, которое не принадлежит текущему издателю.

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

Используйте этот призыв, чтобы узнать, есть ли какие-либо частные или публичные предложения для текущего издателя.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Получить<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   Версия операции, используемой для этого запроса.  |

*Запрос заголовки:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не предусмотрено, он будет сгенерирован и представлен в заголовках ответов. |
|  x-ms-correlationid  | Уникальное строковое значение для операции на стороне клиента. Этот параметр коррелирует все события от работы клиента с событиями на стороне сервера. Если это значение не предусмотрено, он будет сгенерирован и представлен в заголовках ответов. |
|  авторизация     |  [Получить токен для веб-токов JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Например:`Bearer <access_token>`". |

*Коды ответов:*

Код: 200<br>
Получает список доступных планов для клиента. Орган реагирования:

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
Не авторизовано. Токен проверки подлинности не был предоставлен или является недействительным, или запрос пытается получить доступ к приобретению, которое не принадлежит текущему издателю. <br> 

Код: 500<br>
Внутренняя ошибка сервера.<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>Активировать подписку

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Post<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Версия операции, используемой для этого запроса.  |
| subscriptionId     | Уникальный идентификатор подписки SaaS, полученный после разрешения токена с помощью API Resolve.  |

*Запрос заголовки:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не предусмотрено, он будет сгенерирован и представлен в заголовках ответов.  |
|  x-ms-correlationid  | Уникальное строковое значение для операции на стороне клиента. Эта строка коррелирует все события от работы клиента с событиями на стороне сервера. Если это значение не предусмотрено, он будет сгенерирован и представлен в заголовках ответов.  |
|  авторизация     |  [Получить токен для веб-токов JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Например:`Bearer <access_token>`". |

*Запрос полезной нагрузки:*

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
Плохой запрос: сбои проверки.

Код: 403<br>
Не авторизовано. Токен проверки подлинности не был предоставлен или является недействительным, или запрос пытается получить доступ к приобретению, которое не принадлежит текущему издателю.

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

#### <a name="change-the-plan-on-the-subscription"></a>Изменить план по подписке

Обновление плана по подписке.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Обновление<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Версия операции, используемой для этого запроса.  |
| subscriptionId     | Уникальный идентификатор подписки SaaS, полученный после разрешения токена с помощью API Resolve.  |

*Запрос заголовки:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не предусмотрено, он будет сгенерирован и представлен в заголовках ответов.  |
|  x-ms-correlationid  |  Уникальное строковое значение для операции на стороне клиента. Этот параметр коррелирует все события от работы клиента с событиями на стороне сервера. Если это значение не предусмотрено, он будет сгенерирован и представлен в заголовках ответов.    |
| авторизация      |  [Получить токен для веб-токов JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Например:`Bearer <access_token>`".  |

*Запрос полезной нагрузки:*

```json
Request Body:
{
    "planId": "gold"
}
```

*Запрос заголовки:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | Ссылка на ресурс для получения статуса операции.   |

*Коды ответов:*

Код: 202<br>
Запрос на изменение плана принят. Ожидается, что партнер опроводит операцию-место, чтобы определить успех или неудачу. <br>

Код: 400<br>
Плохой запрос: сбои проверки.

Код: 403<br>
Не авторизовано. Токен проверки подлинности не был предоставлен или является недействительным, или запрос пытается получить доступ к приобретению, которое не принадлежит текущему издателю.

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
>Только план или количество могут быть исправлены в одно время, а не оба. Отсечения по подписке `allowedCustomerOperations`с **обновлением** не входит .

#### <a name="change-the-quantity-on-the-subscription"></a>Изменение количества по подписке

Обновление количества по подписке.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Патч:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Версия операции, используемой для этого запроса.  |
| subscriptionId     | Уникальный идентификатор подписки SaaS, полученный после разрешения токена с помощью API Resolve.  |

*Запрос заголовки:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не предусмотрено, он будет сгенерирован и представлен в заголовках ответов.  |
|  x-ms-correlationid  |  Уникальное строковое значение для операции на стороне клиента. Этот параметр коррелирует все события от работы клиента с событиями на стороне сервера. Если это значение не предусмотрено, он будет сгенерирован и представлен в заголовках ответов.    |
| авторизация      |  [Получить токен для веб-токов JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Например:`Bearer <access_token>`".  |

*Запрос полезной нагрузки:*

```json
Request Body:
{
    "quantity": 5
}
```

*Запрос заголовки:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | Ссылка на ресурс для получения статуса операции.   |

*Коды ответов:*

Код: 202<br>
Запрос на изменение количества принят. Ожидается, что партнер опроводит операцию-место, чтобы определить успех или неудачу. <br>

Код: 400<br>
Плохой запрос: сбои проверки.


Код: 403<br>
Не авторизовано. Токен проверки подлинности не был предоставлен или является недействительным, или запрос пытается получить доступ к приобретению, которое не принадлежит текущему издателю.

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
>Только план или количество могут быть исправлены в одно время, а не оба. Отсечения по подписке `allowedCustomerOperations`с **обновлением** не входит .

#### <a name="delete-a-subscription"></a>Удаление подписки

Отменить и удалить указанную подписку.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>DELETE<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Версия операции, используемой для этого запроса.  |
| subscriptionId     | Уникальный идентификатор подписки SaaS, полученный после разрешения токена с помощью API Resolve.  |

*Запрос заголовки:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не предусмотрено, он будет сгенерирован и представлен в заголовках ответов.   |
|  x-ms-correlationid  |  Уникальное строковое значение для операции на стороне клиента. Этот параметр коррелирует все события от работы клиента с событиями на стороне сервера. Если это значение не предусмотрено, он будет сгенерирован и представлен в заголовках ответов.   |
|  авторизация     |  [Получить токен для веб-токов JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Например:`Bearer <access_token>`".  |

*Коды ответов:*

Код: 202<br>
Партнер инициировал звонок, чтобы отписаться от подписки SaaS.<br>

Код: 400<br>
Удалить по подписке с `allowedCustomerOperations` **Удаление** не в .

Код: 403<br>
Не авторизовано. Токен проверки подлинности не был предоставлен или является недействительным, или запрос пытается получить доступ к приобретению, которое не принадлежит текущему издателю.

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


### <a name="operations-api"></a>Операционный API

API операций поддерживает следующие операции патча и получения.

#### <a name="list-outstanding-operations"></a>Список невыполненных операций 

Перечисляет невыполненные операции для текущего издателя. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Получить<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Параметры запроса:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   Версия операции, используемой для этого запроса.                |
| subscriptionId     | Уникальный идентификатор подписки SaaS, полученный после разрешения токена с помощью API Resolve.  |

*Запрос заголовки:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не предусмотрено, он будет сгенерирован и представлен в заголовках ответов.  |
|  x-ms-correlationid |  Уникальное строковое значение для операции на стороне клиента. Этот параметр коррелирует все события от работы клиента с событиями на стороне сервера. Если это значение не предусмотрено, он будет сгенерирован и представлен в заголовках ответов.  |
|  авторизация     |  [Получить токен для веб-токов JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Например:`Bearer <access_token>`".  |

*Коды ответов:*

Код: 200<br> Получает список незавершенных операций по подписке. Полезная нагрузка ответа:

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
Плохой запрос: сбои проверки.

Код: 403<br>
Не авторизовано. Токен проверки подлинности не был предоставлен или является недействительным, или запрос пытается получить доступ к приобретению, которое не принадлежит текущему издателю.

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

#### <a name="get-operation-status"></a>Получить статус операции

Позволяет издателю отслеживать состояние указанной операции `Subscribe`async(например, `ChangePlan`, `ChangeQuantity` `Unsubscribe`или).

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Получить<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Версия операции, используемой для этого запроса.  |

*Запрос заголовки:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не предусмотрено, он будет сгенерирован и представлен в заголовках ответов.  |
|  x-ms-correlationid |  Уникальное строковое значение для операции на стороне клиента. Этот параметр коррелирует все события от работы клиента с событиями на стороне сервера. Если это значение не предусмотрено, он будет сгенерирован и представлен в заголовках ответов.  |
|  авторизация     |  [Получить токен для веб-токов JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Например:`Bearer <access_token>`".  |

*Коды ответов:*<br>

Код: 200<br> Получает указанный в ожидании операции SaaS. Полезная нагрузка ответа:

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
Плохой запрос: сбои проверки.

Код: 403<br>
Не авторизовано. Токен проверки подлинности не был предоставлен или является недействительным, или запрос пытается получить доступ к приобретению, которое не принадлежит текущему издателю.
 
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
#### <a name="update-the-status-of-an-operation"></a>Обновление статуса операции

Обновление состояния операции, чтобы указать на успех или неудачу с предоставленными значениями.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Обновление<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  Версия операции, используемой для этого запроса.  |
| subscriptionId     | Уникальный идентификатор подписки SaaS, полученный после разрешения токена с помощью API Resolve.  |
|  operationId       | Операция, которая завершается. |

*Запрос заголовки:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не предусмотрено, он будет сгенерирован и представлен в заголовках ответов. |
|  x-ms-correlationid |  Уникальное строковое значение для операции на стороне клиента. Этот параметр коррелирует все события от работы клиента с событиями на стороне сервера. Если это значение не предусмотрено, он будет сгенерирован и представлен в заголовках ответов. |
|  авторизация     |  [Получить токен для веб-токов JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Например:`Bearer <access_token>`".  |

*Запрос полезной нагрузки:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Коды ответов:*

Код: 200<br> Звонок для информирования о завершении операции на стороне партнера. Например, этот ответ может свидетельствовать о смене мест или планов.

Код: 400<br>
Плохой запрос: сбои проверки.

Код: 403<br>
Не авторизовано. Токен проверки подлинности не был предоставлен или является недействительным, или запрос пытается получить доступ к приобретению, которое не принадлежит текущему издателю.

Код: 404<br>
Не найдено.

Код: 409<br>
Конфликт. Например, новая транзакция уже выполнена.

Код: 500<br> Внутренняя ошибка сервера.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>Внедрение веб-крючка на сервисе SaaS

Издатель должен внедрить webhook в этом сервисе SaaS, чтобы упреждающе уведомлять пользователей об изменениях в его службе. Ожидается, что служба SaaS вызовет API операций для проверки и авторизации, прежде чем принять решение об уведомлении webhook.


```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher's name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
Где действие может быть одним из следующих: 
- `Unsubscribe`(когда ресурс удален)
- `ChangePlan`(когда операция плана изменений завершена)
- `ChangeQuantity`(когда операция количества изменения завершена)
- `Suspend`(когда ресурс приостановлен)
- `Reinstate`(когда ресурс был восстановлен после приостановки)

Где статус может быть одним из следующих: 
- **NotStarted** <br>
 - **Inprogress** <br>
- **Удалось** <br>
- **Сбой при** <br>
- **Конфликта** <br>

В уведомлении webhook действия статусы либо **успешно** и **не удалось**. Жизненный цикл операции от **NotStarted** до состояния терминала, как **Успешное,** **Неудачное**или **Конфликт.** Если вы получили **NotStarted** или **InProgress,** продолжайте запрашивать статус через GET API до тех пор, пока операция не достигнет состояния терминала, прежде чем принять меры. 

## <a name="mock-apis"></a>Mock APIs

Вы можете использовать наши макеты AIS, чтобы помочь вам начать разработку, в частности, прототипирование, а также проекты тестирования. 

Конечная точка `https://marketplaceapi.microsoft.com/api` пребывания: (не требуется аутентификация)<br/>
Версия API:`2018-09-15`<br/>
Пример URI:`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

Конечные пути API одинаковы как в макете, так и в реальных API, но версии API отличаются. Версия предназначена `2018-09-15` для макетной версии и `2018-08-31` для производственной версии. 

Любой из вызовов API в этой статье может быть сделан в конечную точку макета узла. В общем, ожидать, чтобы получить макет данных обратно в качестве ответа. Вызовы на методы обновления подписки на макете API всегда возвращают 500. 

## <a name="next-steps"></a>Дальнейшие действия

Разработчики также могут программно получать и манипулировать рабочими нагрузками, предложениями и профилями издателей с помощью [AI Cloud Partner Portal REST.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)
