---
title: Добавление соединителей API в потоки для самостоятельной регистрации в Azure AD
description: Настройка веб-API для использования в потоке пользователя.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa6726bb5c60dceab0a58632da99c04361183246
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97932696"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>Добавление соединителя API в поток пользователя

Чтобы использовать [соединитель API](api-connectors-overview.md), сначала создайте соединитель API, а затем включите его в потоке пользователя.

> [!IMPORTANT]
>**Начиная с 4 января 2021 г.** компания Google [прекращает поддержку входа через веб-представление](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Если вы используете федерацию Google или самостоятельную регистрацию с помощью Gmail, вам нужно [проверить нативные бизнес-приложения на совместимость](google-federation.md#deprecation-of-webview-sign-in-support).

## <a name="create-an-api-connector"></a>Создание соединителя API

1. Войдите на [портал Azure](https://portal.azure.com/) с учетной записью администратора Azure AD.
2. В разделе **Службы Azure** щелкните **Azure Active Directory**.
3. В меню слева щелкните **Внешние удостоверения**.
4. Выберите **все соединители API (Предварительная версия)**, а затем выберите **новый соединитель API**.

   ![Добавить новый соединитель API](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. Укажите отображаемое имя для вызова. Например, **Проверьте состояние утверждения**.
6. Укажите **URL-адрес конечной точки** для вызова API.
7. Укажите сведения для проверки подлинности для API.

   - В настоящее время поддерживается только обычная проверка подлинности. Если вы хотите использовать API без обычной проверки подлинности для целей разработки, просто введите фиктивное **имя пользователя** и **пароль** , которые API может игнорировать. Для использования с функцией Azure с ключом API можно включить код в качестве параметра запроса в **URL-адрес конечной точки** (например, HTTPS []() ://contoso.azurewebsites.NET/API/Endpoint <b>? Code = 0123456789</b>).

   ![Настройка нового соединителя API](./media/self-service-sign-up-add-api-connector/api-connector-config.png)
8. Щелкните **Сохранить**.

> [!IMPORTANT]
> Ранее было необходимо было настроить, какие атрибуты пользователей следует отправлять в API ("утверждений для отправки"), и какие атрибуты пользователя следует принимать из API ("утверждений для получения"). Теперь все пользовательские атрибуты отправляются по умолчанию, если в ответе "продолжение" интерфейс API может вернуть любой пользовательский атрибут.

## <a name="the-request-sent-to-your-api"></a>Запрос, отправленный в API
Соединитель API материализации как запрос **HTTP POST** , отправляющий атрибуты пользователя ("Claims") в качестве пар "ключ-значение" в теле JSON. Атрибуты сериализуются аналогично [Microsoft Graph](/graph/api/resources/user#properties) свойства пользователя. 

**Пример запроса**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

В запросе могут быть отправлены только свойства пользователя и настраиваемые атрибуты, перечисленные в  >  пользовательском интерфейсе пользовательских атрибутов Azure Active Directory **внешних удостоверений**  >   .

Пользовательские атрибуты существуют в **extension_ формате \<extensions-app-id> _AttributeName**  в каталоге. API должен рассчитывать на получение утверждений в том же сериализованном формате. Дополнительные сведения о настраиваемых атрибутах см. в разделе [Определение настраиваемых атрибутов для самостоятельной регистрации](user-flow-add-custom-attributes.md).

Кроме того, утверждение **национальных настроек пользовательского интерфейса ("ui_locales")** отправляется по умолчанию во все запросы. Он предоставляет языковые стандарты пользователя, настроенные на устройстве, которые могут использоваться API для возврата международных ответов.

> [!IMPORTANT]
> Если утверждение для отправки не имеет значения во время вызова конечной точки API, утверждение не будет отправлено в API. Ваш API должен быть спроектирован таким образом, чтобы явно проверять предполагаемое значение.

> [!TIP] 
> [**идентификаторы ("удостоверения")**](/graph/api/resources/objectidentity) и **адреса электронной почты ("Электронная почта")** могут использоваться API для идентификации пользователя, прежде чем они будут иметь учетную запись в клиенте. Утверждение "удостоверения" отправляется, когда пользователь проходит проверку подлинности с помощью поставщика удостоверений, например Google или Facebook. "Электронная почта" всегда отправляется.

## <a name="enable-the-api-connector-in-a-user-flow"></a>Включение соединителя API в потоке пользователя

Выполните следующие действия, чтобы добавить соединитель API в поток пользователей самостоятельной регистрации.

1. Войдите на [портал Azure](https://portal.azure.com/) с учетной записью администратора Azure AD.
2. В разделе **Службы Azure** щелкните **Azure Active Directory**.
3. В меню слева щелкните **Внешние удостоверения**.
4. Выберите **потоки пользователей (Предварительная версия)**, а затем выберите пользовательский поток, к которому нужно добавить соединитель API.
5. Выберите **соединители API**, а затем выберите конечные точки API, которые нужно вызвать, на следующих шагах в потоке пользователя:

   - **После входа с помощью поставщика удостоверений**
   - **Перед созданием пользователя**

   ![Добавление API в поток пользователя](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. Щелкните **Сохранить**.

## <a name="after-signing-in-with-an-identity-provider"></a>После входа с помощью поставщика удостоверений

Соединитель API на этом этапе процесса регистрации вызывается сразу после проверки подлинности пользователя с помощью поставщика удостоверений (Google, Facebook, Azure AD). На этом шаге перед *_страницей "Коллекция атрибутов_*" указана форма _, которая представляется пользователю для сбора атрибутов пользователя. 

<!-- The following are examples of API connector scenarios you may enable at this step:
- Use the email or federated identity that the user provided to look up claims in an existing system. Return these claims from the existing system, pre-fill the attribute collection page, and make them available to return in the token.
- Validate whether the user is included in an allow or deny list, and control whether they can continue with the sign-up flow. -->

### <a name="example-request-sent-to-the-api-at-this-step"></a>Пример запроса, отправленного в API на этом шаге
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

Точные утверждения, отправляемые в API, зависят от того, какие сведения предоставляются поставщиком удостоверений. "Электронная почта" всегда отправляется.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Ожидаемые типы ответов из веб-API на этом шаге

Когда веб-API получает запрос HTTP от Azure AD во время потока пользователя, он может вернуть следующие ответы:

- Ответ продолжения
- Блокирующий ответ

#### <a name="continuation-response"></a>Ответ продолжения

Ответ на продолжение указывает на то, что поток пользователя переходит к следующему шагу: странице Коллекция атрибутов.

В ответе на продолжение API может возвращать утверждения. Если в API возвращается утверждение, утверждение выполняет следующие действия:

- Предварительно заполняет поле ввода на странице Коллекция атрибутов.

См. Пример ответа на [продолжение](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Блокирующий ответ

Блокирующий ответ завершает поток пользователя. Он может быть специально выдан API-интерфейсом для прекращения продолжения работы потока пользователя путем отображения страницы блокировки для пользователя. На странице Block отображается `userMessage` предоставленный API-интерфейс.

См. пример [блокирующего ответа](#example-of-a-blocking-response).

## <a name="before-creating-the-user"></a>Перед созданием пользователя

Соединитель API на этом этапе в процессе регистрации вызывается после страницы коллекции атрибутов, если она включена. Этот шаг всегда вызывается перед созданием учетной записи пользователя в Azure AD. 

<!-- The following are examples of scenarios you might enable at this point during sign-up: -->
<!-- 
- Validate user input data and ask a user to resubmit data.
- Block a user sign-up based on data entered by the user.
- Perform identity verification.
- Query external systems for existing data about the user and overwrite the user-provided value. -->

### <a name="example-request-sent-to-the-api-at-this-step"></a>Пример запроса, отправленного в API на этом шаге

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```
Точные утверждения, отправляемые в API, зависят от того, какие сведения собираются от пользователя или предоставлены поставщиком удостоверений.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Ожидаемые типы ответов из веб-API на этом шаге

Когда веб-API получает запрос HTTP от Azure AD во время потока пользователя, он может вернуть следующие ответы:

- Ответ продолжения
- Блокирующий ответ
- Ответ на проверку

#### <a name="continuation-response"></a>Ответ продолжения

Ответ на продолжение указывает, что поток пользователя должен перейти к следующему шагу: Создайте пользователя в каталоге.

В ответе на продолжение API может возвращать утверждения. Если в API возвращается утверждение, утверждение выполняет следующие действия:

- Переопределяет любое значение, которое уже было присвоено утверждению на странице Коллекция атрибутов.

См. Пример ответа на [продолжение](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Блокирующий ответ
Блокирующий ответ завершает поток пользователя. Он может быть специально выдан API-интерфейсом для прекращения продолжения работы потока пользователя путем отображения страницы блокировки для пользователя. На странице Block отображается `userMessage` предоставленный API-интерфейс.

См. пример [блокирующего ответа](#example-of-a-blocking-response).

### <a name="validation-error-response"></a>Проверка-ошибка при отклике
 Когда API отвечает на ответ проверки на ошибку, поток пользователя остается на странице Коллекция атрибутов, а `userMessage` пользователь отображается пользователю. Пользователь может изменить и повторно отправить форму. Этот тип ответа можно использовать для проверки входных данных.

См. Пример ответа на [ошибку проверки](#example-of-a-validation-error-response).

## <a name="example-responses"></a>Примеры ответов

### <a name="example-of-a-continuation-response"></a>Пример ответа продолжения

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| Параметр                                          | Тип              | Обязательно | Описание                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                                            | Строка            | Да      | Версия API.                                                                                                                                                                                                                                                                |
| action                                             | Строка            | Да      | Необходимое значение: `Continue`.                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | Нет       | Значения могут храниться в каталоге, если они были выбраны в качестве *утверждения _ для получения** в конфигурации соединителя API и **пользовательских атрибутов** для потока пользователя. Значения могут возвращаться в токене, если выбрано в качестве **утверждения приложения**.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | Нет       | Возвращаемое утверждение не обязательно должно содержать `_<extensions-app-id>_` . Значения хранятся в каталоге, если они выбраны в качестве **утверждения для получения** в конфигурации соединителя API и **пользовательского атрибута** для потока пользователя. Пользовательские атрибуты не могут быть отправлены обратно в токен. |

### <a name="example-of-a-blocking-response"></a>Пример блокирующего ответа

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
    "code": "CONTOSO-BLOCK-00"
}

```

| Параметр   | Тип   | Обязательно | Описание                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | Строка | Да      | Версия API.                                                    |
| action      | Строка | Да      | Значение должно быть `ShowBlockPage`                                              |
| userMessage | Строка | Да      | Сообщение, отображаемое для пользователя.                                            |
| code        | Строка | Нет       | Код ошибки. Может использоваться в целях отладки. Не отображается для пользователя. |

**Взаимодействие с конечным пользователем с блокирующим ответом**

![Пример блокировки страницы](./media/api-connectors-overview/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>Пример ответа на ошибку проверки

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
    "code": "CONTOSO-VALIDATION-00"
}
```

| Параметр   | Тип    | Обязательно | Описание                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | Строка  | Да      | Версия API.                                                    |
| action      | Строка  | Да      | Необходимое значение: `ValidationError`.                                           |
| status      | Целое число | Да      | `400`Для ответа ValidationError должно быть значение.                        |
| userMessage | Строка  | Да      | Сообщение, отображаемое для пользователя.                                            |
| code        | Строка  | Нет       | Код ошибки. Может использоваться в целях отладки. Не отображается для пользователя. |

**Взаимодействие с пользователем при проверке — ошибка проверки**

![Страница "пример проверки"](./media/api-connectors-overview/validation-error-postal-code.png)


## <a name="best-practices-and-how-to-troubleshoot"></a>Рекомендации и устранение неполадок

### <a name="using-serverless-cloud-functions"></a>Использование безсерверных облачных функций
Бессерверные функции, такие как триггеры HTTP в функциях Azure, предоставляют простой способ создания конечных точек API для использования с соединителем API. Вы можете использовать бессерверную облачную функцию, [например](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts), выполнить логику проверки и ограничить число входов конкретными доменами. Бессерверная облачная функция также может вызывать и вызывать другие веб-API, хранилища пользователей и другие облачные службы для более сложных сценариев.

### <a name="best-practices"></a>Рекомендации
Убедитесь в следующем:
* API-интерфейс следует за контрактами запросов и ответов API, как описано выше. 
* **URL-адрес конечной точки** соединителя API указывает на правильную конечную точку API.
* API явным образом проверяет значения NULL полученных утверждений.
* API реагирует как можно быстрее, чтобы обеспечить работу пользователей в гибком виде.
    * Если используется бессерверная функция или Масштабируемая веб-служба, используйте план размещения, который поддерживает API "спящий" или "тепло". Для функций Azure рекомендуется использовать [план Premium](../../azure-functions/functions-premium-plan.md). 


### <a name="use-logging"></a>Использовать ведение журнала
Как правило, полезно использовать средства ведения журнала, включенные службой веб-API, например [Application Insights](../../azure-functions/functions-monitoring.md), для мониторинга API с непредвиденными кодами ошибок, исключениями и низкой производительностью.
* Отслеживать коды состояния HTTP, которые не являются HTTP 200 или 400.
* Код состояния HTTP 401 или 403, как правило, указывает на наличие проблемы с проверкой подлинности. Дважды проверьте уровень проверки подлинности API и соответствующую конфигурацию в соединителе API.
* При необходимости используйте более агрессивные уровни ведения журнала (например, "Трассировка" или "Отладка") в разработке.
* Отслеживайте долгое время ответа API.

## <a name="next-steps"></a>Дальнейшие действия
<!-- - Learn [where you can enable an API connector](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow) -->
- Узнайте, как [Добавить пользовательский рабочий процесс утверждения для самостоятельной регистрации](self-service-sign-up-add-approvals.md)
- Приступая к работе с [примерами краткого руководства по функциям Azure](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).
<!-- - Learn how to [use API connectors to verify a user identity](code-samples-self-service-sign-up.md#identity-verification) -->