---
title: V2 реализации API SaaS | Azure Marketplace
description: В этой статье объясняется, как создавать и управлять ими на предложение SaaS на AppSource и Azure Marketplace с помощью связанных исполнение v2 API-интерфейсы.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: f5be0b8886500bdce50b95846826e5fdc53b5df1
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202661"
---
# <a name="saas-fulfillment-apis-version-2"></a>Выполнение SaaS API-интерфейсы, версия 2 

В этой статье описаны интерфейсы API, которые партнеры могли продавать свои приложения SaaS в AppSource marketplace и в Azure Marketplace. Эти интерфейсы API не является обязательным для могут быть частью транзакции SaaS предложения в Azure Marketplace и AppSource.

## <a name="managing-the-saas-subscription-life-cycle"></a>Управление жизненным циклом подписки SaaS

Azure SaaS управляет весь жизненный цикл покупки подписки SaaS. Он использует реализации API-интерфейсы в качестве механизма для фактического выполнения, изменения планов и удаление подписки с партнером. Счет клиента за основан на состояние подписки SaaS, которую корпорация Майкрософт поддерживает. На следующей схеме показано состояний и операций, которые управляют изменения между состояниями.

![Состояния жизненного цикла подписки SaaS](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Состояния подписки SaaS

Ниже перечислены состояния инициализации для подписки SaaS, включая описание и последовательности схема для каждого (если применимо). 

#### <a name="provisioning"></a>Подготовка

Когда покупатель начинает покупки, партнер получает эти сведения в код авторизации клиента интерактивных веб-страницы, использующий параметр URL-адреса. Например, `https://contoso.com/signup?token=..`, а URL-адрес целевой страницы в центре партнеров — `https://contoso.com/signup`. Код авторизации можно проверить и обмен Дополнительные сведения о службе подготовки, путем вызова API устранения.  По завершении подготовки службы SaaS, он отправляет вызов активировать для обозначения завершения его выполнения и может быть выставлен счет клиента. 

Следующая диаграмма показывает последовательность вызовов API для подготовки сценария.  

![Вызовы API для подготовки службы SaaS](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Подготовлено

Это состояние в рабочем режиме подготовленной службы.

##### <a name="provisioning-for-update"></a>Подготовка для обновления 

Это состояние означает, что обновление для существующей службы находится в состоянии ожидания. Такое обновление может быть инициировано клиента, из marketplace или в службе SaaS (только для транзакций direct клиенту).

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>Подготовка для обновления (если он инициируется из marketplace)

В примере ниже показан последовательность действий при инициации обновления из marketplace.

![Вызовы API, когда обновление инициируется из marketplace](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>Подготовка для обновления (если он был запущен в службе SaaS)

В примере ниже показан действия при инициации обновления в службе SaaS. (Вызов веб-перехватчика заменяется на обновление подписки, инициированных одной службой SaaS). 

![Вызовы API при инициации обновления от службы SaaS](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Приостановлено

Это состояние указывает, что платеж пользователя не были получены. С помощью политики мы предоставим льготный период перед отменой подписки клиента. Если подписка находится в этом состоянии: 

- Как партнер вы можете привести к снижению или запретить доступ пользователя к службе.
- Подписка должна оставаться в состоянии, допускающем восстановление, можно восстановить полную функциональность без потери данных или параметров. 
- Ожидать возвращения запрос get для этой подписки по реализации API-интерфейсы или отмены подготовки запроса в конце этого периода. 

#### <a name="unsubscribed"></a>Подписка отменена 

Подписки попадает в эту ситуацию, в ответ на запрос в службу явные или неуплаты из dues. От партнера, ожидается, что данные клиента хранятся для восстановления по запросу в течение определенного числа дней и затем удаляется. 


## <a name="api-reference"></a>Справочник по API

В данном разделе описываются SaaS *подписки API* и *операций API*.  Значение `api-version` параметр версии 2 API-интерфейсов является `2018-08-31`.  


### <a name="parameter-and-entity-definitions"></a>Определения параметров и сущности

В следующей таблице перечислены определения общих параметров и сущности, используемые в реализации API-интерфейсы.

|     Параметр/субъекта     |     Определение                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | Идентификатор GUID для ресурсов SaaS.  |
| `name`                   | Понятное имя, предоставленных клиентом для этого ресурса. |
| `publisherId`            | Уникальный строковый идентификатор для каждого издателя (например: «contoso»). |
| `offerId`                | Уникальный строковый идентификатор для каждого предложения (например: «offer1»).  |
| `planId`                 | Уникальный строковый идентификатор для каждого плана или номера SKU (например: «серебро»). |
| `operationId`            | Идентификатор GUID для конкретной операции.  |
|  `action`                | Действие, выполняемое для ресурса, либо `subscribe`, `unsubscribe`, `suspend`, `reinstate`, или `changePlan`, `changeQuantity`, `transfer`.  |
|   |   |

Глобальные уникальные идентификаторы ([идентификаторы GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)) являются числами 128-разрядный (32-шестнадцатеричном формате), которые обычно формируется автоматически. 

#### <a name="resolve-a-subscription"></a>Разрешить подписки 

Разрешение конечной точки позволяет издателю разрешить маркер marketplace в идентификатор ресурса постоянные. Идентификатор ресурса имеет уникальный идентификатор для подписки SaaS. Когда пользователь перенаправляется на веб-сайт партнера, URL-адрес содержит токен в параметрах запроса. Партнер должен использовать этот маркер и сделать запрос на ее устранения. Ответ содержит уникальный идентификатор подписки SaaS, имя, идентификатор предложения и план для ресурса. Этот маркер действителен в течение только одного часа. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Поместить<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Версия операции, используемой для этого запроса.  |

*Заголовки запроса:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не указано, один будет сгенерирован и в заголовке ответа. |
|  x-ms-correlationid |  Уникальное строковое значение для операции на стороне клиента. Этот параметр сопоставляет все события из клиентской операции с событиями на стороне сервера. Если это значение не указано, один будет сгенерирован и в заголовке ответа.  |
|  authorization     |  [Получение токена JSON web token (JWT) носителя](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). |
|  x-ms-marketplace-token  |  Параметр маркера запроса в URL-АДРЕСЕ, когда пользователь перенаправляется на веб-сайт партнера SaaS с Azure (например: `https://contoso.com/signup?token=..`). *Примечание.* URL-адрес декодирует значение токена из браузера перед его использованием.  |

*Коды ответов:*

Код: 200<br>
Разрешает непрозрачного токена на подписку SaaS. Текст ответа:
 

```json
{
    "subscriptionId": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

Код: 400<br>
Недопустимый запрос. x-ms-marketplace-token отсутствует, имеет неправильный формат или не истекшим сроком действия.

Код: 403<br>
Не авторизовано. Маркер проверки подлинности не указан или является недопустимым, или запрос пытается получить доступ к приобретения, не принадлежащие к текущего издателя.

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

Данная подписка API поддерживает следующие операции HTTPS: **Получить**, **Post**, **Patch**, и **удалить**.


#### <a name="list-subscriptions"></a>Список подписок

Список всех подписок SaaS для издателя.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Получить<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Параметры запроса:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  Версия операции, используемой для этого запроса.  |

*Заголовки запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не указано, один будет сгенерирован и в заголовке ответа. |
| x-ms-correlationid |  Уникальное строковое значение для операции на стороне клиента. Этот параметр сопоставляет все события из клиентской операции с событиями на стороне сервера. Если это значение не указано, один будет сгенерирован и в заголовке ответа.  |
| authorization      |  [Получение токена JSON web token (JWT) носителя](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Коды ответов:*

Код: 200 <br/>
Получает издателя и соответствующих подписок для предложения издателя, на основе токена проверки подлинности.
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
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

Маркер продолжения будет присутствовать только в том случае, если существуют дополнительные «страницы» планов для извлечения. 

Код: 403 <br>
Не авторизовано. Маркер проверки подлинности не указан или является недопустимым, или запрос пытается получить доступ к приобретения, не принадлежащие к текущего издателя. 

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

Возвращает указанную подписку SaaS. Используйте этот вызов для получения сведений о лицензиях и сведения о плане.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Получить<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Уникальный идентификатор, полученный после устранения маркер с помощью API устранения подписки SaaS.   |
|  ApiVersion        |   Версия операции, используемой для этого запроса.   |

*Заголовки запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не указано, один будет сгенерирован и в заголовке ответа. |
|  x-ms-correlationid |  Уникальное строковое значение для операции на стороне клиента. Этот параметр сопоставляет все события из клиентской операции с событиями на стороне сервера. Если это значение не указано, один будет сгенерирован и в заголовке ответа.  |
|  authorization     |  [Получение токена JSON web token (JWT) носителя](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). |

*Коды ответов:*

Код: 200<br>
Возвращает SaaS подписку из идентификатора. Полезные данные ответа:<br>

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
        "status": "Subscribed", // Indicates the status of the operation.
}
```

Код: 403<br>
Не авторизовано. Маркер проверки подлинности не указан или является недопустимым, или запрос пытается получить доступ к приобретения, не принадлежащие к текущего издателя.

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

Используйте этот вызов, чтобы узнать, есть ли предложениями частного или общедоступного для текущего издателя.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Получить<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   Версия операции, используемой для этого запроса.  |

*Заголовки запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не указано, один будет сгенерирован и в заголовке ответа. |
|  x-ms-correlationid  | Уникальное строковое значение для операции на стороне клиента. Этот параметр сопоставляет все события из клиентской операции с событиями на стороне сервера. Если это значение не указано, один будет сгенерирован и в заголовке ответа. |
|  authorization     |  [Получение токена JSON web token (JWT) носителя](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). |

*Коды ответов:*

Код: 200<br>
Получает список доступных планов для клиента. Текст ответа:

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
Не авторизовано. Маркер проверки подлинности не указан или является недопустимым, или запрос пытается получить доступ к приобретения, не принадлежащие к текущего издателя. <br> 

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

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Поместить<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Версия операции, используемой для этого запроса.  |
| subscriptionId     | Уникальный идентификатор, полученный после устранения маркер с помощью API устранения подписки SaaS.  |

*Заголовки запроса:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не указано, один будет сгенерирован и в заголовке ответа.  |
|  x-ms-correlationid  | Уникальное строковое значение для операции на стороне клиента. Эта строка сопоставляет все события из клиентской операции с событиями на стороне сервера. Если это значение не указано, один будет сгенерирован и в заголовке ответа.  |
|  authorization     |  [Получение токена JSON web token (JWT) носителя](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). |

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
Недопустимый запрос: сбои при выполнении проверки.

Код: 403<br>
Не авторизовано. Маркер проверки подлинности не указан или является недопустимым, или запрос пытается получить доступ к приобретения, не принадлежащие к текущего издателя.

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

#### <a name="change-the-plan-on-the-subscription"></a>Изменить план на подписку.

Обновите план для подписки.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Исправление<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Версия операции, используемой для этого запроса.  |
| subscriptionId     | Уникальный идентификатор, полученный после устранения маркер с помощью API устранения подписки SaaS.  |

*Заголовки запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не указано, один будет сгенерирован и в заголовке ответа.  |
|  x-ms-correlationid  |  Уникальное строковое значение для операции на стороне клиента. Этот параметр сопоставляет все события из клиентской операции с событиями на стороне сервера. Если это значение не указано, один будет сгенерирован и в заголовке ответа.    |
| authorization      |  [Получение токена JSON web token (JWT) носителя](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

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
| Operation-Location | Ссылка на ресурс, чтобы получить состояние операции.   |

*Коды ответов:*

Код: 202<br>
Принял запрос на изменение плана. Партнер должен опросить Operation-Location, чтобы определить успех или неудачу. <br>

Код: 400<br>
Недопустимый запрос: сбои при выполнении проверки.

Код: 403<br>
Не авторизовано. Маркер проверки подлинности не указан или является недопустимым, или запрос пытается получить доступ к приобретения, не принадлежащие к текущего издателя.

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
>План или количество может быть исправлен за один раз, но не оба. Редактирует на подписки с помощью **обновление** не находится в `allowedCustomerOperations`.

#### <a name="change-the-quantity-on-the-subscription"></a>Измените количество на подписку.

Обновление количества в подписке.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Исправление:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Версия операции, используемой для этого запроса.  |
| subscriptionId     | Уникальный идентификатор, полученный после устранения маркер с помощью API устранения подписки SaaS.  |

*Заголовки запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не указано, один будет сгенерирован и в заголовке ответа.  |
|  x-ms-correlationid  |  Уникальное строковое значение для операции на стороне клиента. Этот параметр сопоставляет все события из клиентской операции с событиями на стороне сервера. Если это значение не указано, один будет сгенерирован и в заголовке ответа.    |
| authorization      |  [Получение токена JSON web token (JWT) носителя](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

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
| Operation-Location | Ссылки на ресурс, чтобы получить состояние операции.   |

*Коды ответов:*

Код: 202<br>
Запрос, чтобы изменить количество принят. Партнер должен опросить Operation-Location, чтобы определить успех или неудачу. <br>

Код: 400<br>
Недопустимый запрос: сбои при выполнении проверки.


Код: 403<br>
Не авторизовано. Маркер проверки подлинности не указан или является недопустимым, или запрос пытается получить доступ к приобретения, не принадлежащие к текущего издателя.

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
>План или количество может быть исправлен за один раз, но не оба. Редактирует на подписки с помощью **обновление** не находится в `allowedCustomerOperations`.

#### <a name="delete-a-subscription"></a>Удаление подписки

Отказаться от подписки и удаление указанной подписки.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionid-api-versionapiversion"></a>Delete (Удалить)<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Версия операции, используемой для этого запроса.  |
| subscriptionId     | Уникальный идентификатор, полученный после устранения маркер с помощью API устранения подписки SaaS.  |

*Заголовки запроса:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не указано, один будет сгенерирован и в заголовке ответа.   |
|  x-ms-correlationid  |  Уникальное строковое значение для операции на стороне клиента. Этот параметр сопоставляет все события из клиентской операции с событиями на стороне сервера. Если это значение не указано, один будет сгенерирован и в заголовке ответа.   |
|  authorization     |  [Получение токена JSON web token (JWT) носителя](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Коды ответов:*

Код: 202<br>
Партнер инициировала вызов, чтобы отказаться от подписки подписка SaaS.<br>

Код: 400<br>
Удалить на подписки с помощью **удалить** не в `allowedCustomerOperations`.

Код: 403<br>
Не авторизовано. Маркер проверки подлинности не указан или является недопустимым, или запрос пытается получить доступ к приобретения, не принадлежащие к текущего издателя.

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


### <a name="operations-api"></a>Операции API

Операции API поддерживает следующие операции исправления и Get.

#### <a name="list-outstanding-operations"></a>Список незавершенных операций 

Список незавершенных операций для текущего издателя. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Получить<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Параметры запроса:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   Версия операции, используемой для этого запроса.                |
| subscriptionId     | Уникальный идентификатор, полученный после устранения маркер с помощью API устранения подписки SaaS.  |

*Заголовки запроса:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не указано, один будет сгенерирован и в заголовке ответа.  |
|  x-ms-correlationid |  Уникальное строковое значение для операции на стороне клиента. Этот параметр сопоставляет все события из клиентской операции с событиями на стороне сервера. Если это значение не указано, один будет сгенерирован и в заголовке ответа.  |
|  authorization     |  [Получение токена JSON web token (JWT) носителя](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Коды ответов:*

Код: 200<br> Получает список ожидающих операций для подписки. Полезные данные ответа:

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
Недопустимый запрос: сбои при выполнении проверки.

Код: 403<br>
Не авторизовано. Маркер проверки подлинности не указан или является недопустимым, или запрос пытается получить доступ к приобретения, не принадлежащие к текущего издателя.

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

#### <a name="get-operation-status"></a>Получить состояние операции

Позволяет издателю для отслеживания состояния указанного активированные асинхронной операции (такие как `subscribe`, `unsubscribe`, `changePlan`, или `changeQuantity`).

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Получить<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Версия операции, используемой для этого запроса.  |

*Заголовки запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не указано, один будет сгенерирован и в заголовке ответа.  |
|  x-ms-correlationid |  Уникальное строковое значение для операции на стороне клиента. Этот параметр сопоставляет все события из клиентской операции с событиями на стороне сервера. Если это значение не указано, один будет сгенерирован и в заголовке ответа.  |
|  authorization     |  [Получение токена JSON web token (JWT) носителя](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Коды ответов:*<br>

Код: 200<br> Возвращает указанный ожидающей операции SaaS. Полезные данные ответа:

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
Недопустимый запрос: сбои при выполнении проверки.

Код: 403<br>
Не авторизовано. Маркер проверки подлинности не указан или является недопустимым, или запрос пытается получить доступ к приобретения, не принадлежащие к текущего издателя.
 
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
#### <a name="update-the-status-of-an-operation"></a>Обновить состояние операции

Обновите состояние операции, чтобы указать успех или неудачу с предоставленными значениями.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Исправление<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Параметры запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  Версия операции, используемой для этого запроса.  |
| subscriptionId     | Уникальный идентификатор, полученный после устранения маркер с помощью API устранения подписки SaaS.  |
|  operationId       | Операция, которую было выполнено. |

*Заголовки запроса:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   Уникальное строковое значение для отслеживания запроса клиента, желательно GUID. Если это значение не указано, один будет сгенерирован и в заголовке ответа. |
|  x-ms-correlationid |  Уникальное строковое значение для операции на стороне клиента. Этот параметр сопоставляет все события из клиентской операции с событиями на стороне сервера. Если это значение не указано, один будет сгенерирован и в заголовке ответа. |
|  authorization     |  [Получение токена JSON web token (JWT) носителя](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Полезные данные запроса:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Коды ответов:*

Код: 200<br> Вызов для информирования о завершении операции на стороне партнера. Например этот ответ могут сигнализировать о изменение рабочих мест или планы.

Код: 400<br>
Недопустимый запрос: сбои при выполнении проверки.

Код: 403<br>
Не авторизовано. Маркер проверки подлинности не указан или является недопустимым, или запрос пытается получить доступ к приобретения, не принадлежащие к текущего издателя.

Код: 404<br>
Не найдено.

Код: 409<br>
Конфликт. Например новая транзакция уже выполняется.

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

Издатель должен реализовывать веб-перехватчика в эту службу SaaS, чтобы заранее уведомить пользователей об изменениях в свою службу. Ожидается, что служба SaaS вызов операций API для проверки и авторизации перед выполнением действия на веб-перехватчика уведомлений.

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
Когда действие может принимать одно из следующих: 
- `subscribe` (если ресурс был активирован)
- `unsubscribe` (если ресурс был удален)
- `changePlan` (после завершения операции изменения плана)
- `changeQuantity` (после завершения операции изменения количество)
- `suspend` (если ресурс был приостановлен)
- `reinstate` (когда после приостановки был восстановлен ресурсов)

Где состояние может принимать одно из следующих: 
- **NotStarted** <br>
 - **Выполняется** <br>
- **Успешно** <br>
- **Сбой** <br>
- **Конфликт** <br>

В уведомлении веб-перехватчика, практические состояния: либо **Succeeded** и **Failed**. Операции жизненного цикла — от **NotStarted** состоянию как **Succeeded**, **Failed**, или **конфликт**. Если вы получили **NotStarted** или **InProgress**, продолжать запрашивать состояние через API получения, пока операция достигнет конечного состояния перед выполнением действия. 

## <a name="mock-apis"></a>Макет API

Чтобы помочь вам приступить к работе с разработкой, особенно создания прототипов, а также оценки проектов можно использовать наш макет API. 

Размещения конечной точки: `https://marketplaceapi.microsoft.com/api` (проверка подлинности не требуется)<br/>
Версия API: `2018-09-15`<br/>
Пример URI. `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

Пути к конечной точке API одинаковы для всех макетов и реальных API, но имеют разные версии API. Версия `2018-09-15` для имитационную версию и `2018-08-31` для рабочей версии. 

Любой из вызовов API в этой статье может быть сделан для конечной точки макет узла. Как правило надеяться на получение фиктивные данные назад в качестве отклика. Вызовы методов обновления подписки на макет API всегда возвращает 500. 

## <a name="next-steps"></a>Дальнейшие действия

Разработчики могут программным путем получения и обработки рабочих нагрузок, предложения и издателе профилей с помощью [API-интерфейсов REST портал Cloud Partner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
